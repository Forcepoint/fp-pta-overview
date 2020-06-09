# PTA Overview

## Table of Contents

1. [Summary](README.md)
1. [The Need](the_need.md)
1. [Architecture](architecture.md)
1. [Applications](applications.md)
1. [Benefits](benefits.md)
1. [Initial Setup](initial_setup.md)
1. [Building Blocks](building_blocks.md)
1. __[Create Your Hosts](create_your_hosts.md)__

## Create Your Hosts

This is finally the part you've been waiting for. You know the process and the principles behind the process.
You've been provided a number of building blocks, and examples on how to wire things together.
Here are some further notes or gotchas that I wanted to share, as well as a few recipes for hosts that I've used.
The rest is up to you to decide how you want to use PTA. Happy trails! Drop me a line if you're so inclined at 
[jeremy.cornett@forcepointgov.com](mailto:jeremy.cornett@forcepointgov.com).

---

### Notes

1. When you craft your own hosts, you should start with the code in https://github.com/Forcepoint/fp-pta-host-ptacontroller-node-01
as a template. When you add those files to your git repo, ensure inventory.py has execute permissions.

        git update-index --chmod=+x inventory.py

1. Note that if you want to destroy a VM and create it anew with your PTA Controller, simply run the Jenkins job
and set the parameters appropriately. This is the single coolest and awesome ability of PTA. You can 
destroy and recreate a VM setup by PTA in minutes just with the push of a button. Even your production
applications!

    * Enable `Terraform_Taint`
    * Enable `Terraform_Apply`
    * You may or may not want to Disable `Terraform_Verify`.
    * Enable `Ansible`

1. Directions for creating a CSR and obtaining a certificate are here https://github.com/Forcepoint/fp-pta-host-ptacontroller-master/tree/master/files

1. You have to be careful when you reprovision VMs if Artifactory is your Terraform backend. 
Reprovision the Artifactory VM by itself and not in conjunction with any others simultaneously. 
Otherwise those jobs will fail because Artifactory will be unavailable.

1. Allow Rolling Updates

    When it comes to system packages and other like things, I like to enable rolling updates
    (install the latest of everything). For instance, notice that I don't use ansible from a yum package. 
    I always use it from a pip package. This method
    gives me the most up-to-date ansible version as the ones available through YUM on CentOS tend to lag behind.
    This guarantees that I detect problems early, and 9 times out of 10 it doesn't cause an issue other than 
    a failed ansible playbook run. All of these systems are either backed up (repeatedly) or 100% disposable, so the risk is
    very minimal while the benefit of early problem detection is huge.

1. Play Your Cards Close to the Chest

    It would be wise to clone all of the ansible roles and terraform modules into your own git hosting provider,
    or at the very least, instead of referring to "master" so you get rolling updates, you should probably
    lock to a specific version. Sometimes the ansible roles and terraform modules have breaking changes that
    you don't want to take blindly.

1. If you have a need to audit that your systems are actually producing backups, my solution was to create
a Jenkins job that connects to each system and verifies that the
correct number of backups are there and that none are missing (EX: one per workday M-F for 10 workdays).
I was lazy and decided to add each of these applications as a jenkins node to the PTA Controller 
to make the connection via a Jenkinsfile to run a python script for verifying the backups.

---

### Recipes

Here's a few suggested recipes to help illuminate how the building blocks can be pieced together.

1. Jenkins JNLP Build Node for Virtualbox Builds

   The jenkins role needs to be applied before virtualbox so the jenkins user exists as that's the default user for virtualbox.

    * general-config
    * jfrog-cli
    * packer
    * gnome
    * jenkins-node-jnlp
    * virtualbox 

1. Jenkins SSH Build Node for Docker Builds

    * general-config
    * jfrog-cli
    * docker-host
    * jenkins-node-ssh

   I find this to be a useful play for after applying the docker-host role. 
   I set the Jenkins job to run three times a week during the night, 
   which then cleans out all the docker images on that VM, avoiding problems with running out of disk space.

        - name: cleanup docker
          hosts: Docker01
          become: yes
          tasks:
            - name: stop all containers
              shell: docker stop $(docker ps -a -q)
              ignore_errors: yes
        
            - name: remove all stopped containers
              shell: docker rm $(docker ps -a -q)
              ignore_errors: yes
        
            - name: remove all images
              shell: docker rmi $(docker images -q) --force
              ignore_errors: yes
        
1. Gitlab

    Assuming you put all your PTA related code in your Gitlab instance, 
    be sure you don't reprovision your Gitlab instance while reprovisioning any other systems.
    If Gitlab is down, the other Jenkins jobs on PTA Controller won't be able to clone/fetch 
    and will then fail.
    
    Another thing to note is the Jenkinsfile. You should separate the Ansible Galaxy commands
    into their own stage and place it before running Terraform. This ensure that if you
    reprovision the host, the Galaxy commands won't fail because it won't be able to clone/fetch.
    
        stage('Ansible Galaxy') {
            when {
                expression { params.Ansible }
            }
            steps {
                sh '''
                    virtualenv virt_ansible
                    source virt_ansible/bin/activate
                    pip install -r requirements.txt
                    ansible-galaxy install --role-file=requirements.yml --force --roles-path=./roles
                    deactivate
                   '''
            }
        }

    * change-sshd-port - Note that you must set gather_facts to no so it won't error out because 
    the SSH port 2222 isn't set yet on first run. `inventory.py` has been hard coded to set the 
    SSH port to 2222 for any host with 'gitlab' in the name.

            - name: change ssh port
              hosts: gitlab
              gather_facts: no
              roles:
                - role: change-sshd-port

    * general-config
    * docker-host
    * extra-disk
    * docker-gitlab
    * backup-local for `backup_local_target: /mnt/extra/docker/gitlab/config`
    * backup-local for `backup_local_target: /mnt/extra/docker/gitlab/data`

1. Artifactory

    * general-config
    * docker-host
    * extra-disk
    * docker-artifactory
    
    Note that I don't use backup-local here as as Artifactory can manage its own backups. 

    If Artifactory is acting as your backup of your state files, you should be equally concerned 
    about Artifactory's state file.
    You could just leave it alone on the file system of the PTAController. The drawback though
    is that Jenkins will on occasion delete a workspace directory if it's really old.
    My solution was to add a stage to the Artifactory's Jenkinsfile that backed up the tfstate file.
    
            stage('Terraform Backup') {
                when {
                    expression { params.Terraform_Taint || params.Terraform_Untaint || params.Terraform_Apply }
                }
                steps {
                    dir("../terraform_backups_$JOB_BASE_NAME") {
                        sh 'touch terraform.tfstate'
                        sh 'rm terraform.tfstate*'
                        sh "cp $workspace/terraform.tfstate* ./"
                    }
                }
            }

1. PRTG

    Make sure you note that you must create a `hosts` file and modify the `Jenkinsfile` 
    ansible command to use it. See https://github.com/Forcepoint/fp-pta-host-template-windows-10-prod
    as an example.

    * general-config
    * extra-disk-win
    * prtg
    * backup-local-win for `backup_local_win_target: C:\Program Files (x86)\PRTG Network Monitor`
    * backup-local-win for `backup_local_win_target: C:\ProgramData\Paessler\PRTG Network Monitor`
    * backup-local-win for `backup_local_win_target: HKLM\SOFTWARE\Wow6432Node\Paessler\PRTG Network Monitor`

1. TestSystems

    I can say enough how awesome it is to have a TestSystems git repo that runs Terraform and Ansible
    for a collection of test hosts together. This makes upgrading your applications that much more reliable.
    Please, please, please use PTA to create test systems that mock your production applications. This is
    so easy to do with PTA it's nearly a crime not to.

1. Test01

    When I develop new ansible roles for PTA, I like to iterate quickly. It's helpful to just have a single
    VM hanging around that's only had the general-config applied to it for my quick testing and development.
    I highly recommend having such a host. It will make your life as a PTA admin that much better.
    I fully expect that you'll come up with ansible roles that I didn't think of which you'll want to
    create and develop. This VM is the kitchen.

##### ..._Fin_.

Thank you for making it to the end here. I've worked very hard over the years to develop PTA and
then to open source it. I sincerely hope it will help you and your organization.

_Send feedback and comments to [jeremy.cornett@forcepointgov.com](mailto:jeremy.cornett@forcepointgov.com) Forcepoint 2020, BSD-3-Clause_