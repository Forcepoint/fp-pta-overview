# Overview

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

1. Give quick examples of other ways you can combine the roles to produce functional
systems.

1. For each PTA system you provision, you specify a static IP for terraform to use. It is assumed
that you work with your IT department to get static DNS entries created for these applications.

1. Call out the difference in the Jenkinsfile between a gitlab instance and an artifactory instance.

1. If you copy all of the PTA code into your Gitlab instance (which isn't a terrible idea),
be sure you don't reprovision any other hosts while you do your gitlab instance because
if Gitlab is down, the other Jenkins jobs on PTA Controller won't be able to clone/fetch 
and will then fail.

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
 
 1. Need to audit backups. I opted for a Jenkins job that connects to each system and verifies that the
correct number of backups are there and that none are missing (EX: one per workday M-F for 10 workdays).
I was lazy and decided to add each of these applications as a jenkins node to make the connection 
via a Jenkinsfile to run a python script for verifying the backups.