# Overview

## Main Topics

1. Go over the overarching architecture of the CM trifecta. Why this approach is beneficial.
Give warning that this is not trivial or with batteries included. It will save you tons of time
though, and you gain the benefits of using a system that's been proven through development and
active use for 3 years now.

1. Solicit the use of PRTG for monitoring.

1. Go through each of the example hosts in the order someone should set them up so they get
familiar with the tools.

## Notes

1. For each PTA system you provision, you specify a static IP for terraform to use. It is assumed
that you work with your IT department to get static DNS entries created for these applications.

1. Explain about using a secondary disk, especially with a datastore cluster (it is not ideal and can cause DRS headaches).
The idea is for the secondary disk to house the application data and backups. Example...

        - name: setup extra disk
          hosts: artifactory
          vars:
            extra_disk_mount_path: /home/{{ ansible_user_id }}/data
          roles:
          - role: extra-disk
        
        - name: configure Artifactory
          hosts: gitlab
          vars:
            docker_artifactory_data: /home/{{ ansible_user_id }}/data
          roles:
          - role: docker-artifactory
            
    or...
    
        - name: setup extra disk
          hosts: gitlab
          vars:
            extra_disk_mount_path: /mnt/extra
          roles:
            - role: extra-disk
        
        - name: setup gitlab
          hosts: gitlab
          vars:
            docker_gitlab_root_data_dir: /mnt/extra/docker
          roles:
            - role: docker-gitlab
        
        - name: backup the gitlab config
          hosts: gitlab
          vars:
            backup_local_name: gitlab_config
            backup_local_target: /mnt/extra/docker/gitlab/config
            backup_local_destination: /mnt/extra/backups
            # Ensure the two gitlab backups aren't running concurrently by offsetting them by 2 minutes
            backup_local_minute: "0"
          roles:
            - role: backup-local
        
        - name: backup the gitlab data
          hosts: gitlab
          vars:
            backup_local_name: gitlab_data
            backup_local_target: /mnt/extra/docker/gitlab/data
            backup_local_destination: /mnt/extra/backups
            # Ensure the two gitlab backups aren't running concurrently by offsetting them by 2 minutes
            backup_local_minute: "2"
          roles:
            - role: backup-local

1. Need to audit backups. I opted for a Jenkins job that connects to each system and verifies that the
correct number of backups are there and that none are missing (EX: one per workday M-F for 10 workdays).
I was lazy and decided to add each of these applications as a jenkins node to make the connection 
via a Jenkinsfile to run a python script for verifying the backups.

1. Call out the drawbacks of using your own CA instead of a public one.

1. Call out the difference in the ansible.cfg file in pta-controller-master versus pta-controller-node-01.

        vault_password_file = /mnt/extra/service/vault_password.txt

1. Call out the difference in the Jenkinsfile between a gitlab instance and an artifactory instance.

1. For your applications that need a secondary disk, you should take special care to plan for how
much space you'll need. You can always add a bigger disk and copy files over, but that's painful.
Since the disks are all thin anyways, planning that you'll go big eventually will save you some headache.
I suggest that for Artifactory, your second_disk_size be 3000 (GB).

1. If Artifactory is your terraform backend...
 
    1. You have to be careful when you reprovision VMs. 
    Reprovision the Artifactory VM by itself and not in conjunction with any others simultaneously. 
    Otherwise those jobs will fail because Artifactory will be unavailable.

    1. Artifactory is acting as your backup of your state files, but what about Artifactory's state file?
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

1. Call out the drawback that if you host things on your own servers, you can get into a chicken-and-egg
problem if the gitlab provisioning fails.

1. I find this to be a useful play on a VM that serves as a Jenkins build node which does docker builds.
I set the Jenkins job to run three times a week, which then cleans out all the docker images on that VM,
avoiding problems with running out of disk space.

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
        
 1. If different systems have dependencies on eachother like a Jenkins master to a Jenkins build node,
 I make sure the weekly cron schedule in the Jenkinsfile doesn't overlap.
 
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
 
  
 