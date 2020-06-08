# PTA Overview

## Table of Contents

1. [Summary](README.md)
1. [The Need](the_need.md)
1. __[Architecture](architecture.md)__
1. [Applications](applications.md)
1. [Benefits](benefits.md)
1. [Initial Setup](initial_setup.md)
1. [Building Blocks](building_blocks.md)
1. [Create Your Hosts](create_your_hosts.md)

## Architecture

The tech stack used in PTA is heavily influenced by the problems I was trying to solve. 
Combining these tools or concepts provide for the architecture of PTA.

1. Docker
1. Ansible
1. Terraform
1. Packer
1. Secondary Disk

---

### Docker

Docker at it’s heart is a means to abstract as much or as little of a running system or application as you want.

![Docker logo](https://upload.wikimedia.org/wikipedia/commons/7/79/Docker_%28container_engine%29_logo.png)

<a href="https://commons.wikimedia.org/wiki/File:Docker_(container_engine)_logo.png" title="via Wikimedia Commons">dotCloud, Inc.</a> / <a href="http://www.apache.org/licenses/LICENSE-2.0">Apache License 2.0</a>

At the time when I envisioned PTA, my experience with Docker was limited, 
but I knew that most vendors provide their application as a docker image either for 
production use or as means for folks to quickly and easily test out their application. 
In doing so, the vendor solves a number of problems that plague system administrators.

1. __Backups__ - The application data is readily identified by the vendor. They want you to 
be able to reliably use their system, so they tell you what parts of the application you need 
to mount on the host system so your application can survive a restart. This solves the problem 
of identifying what to backup for update rollbacks or disaster recovery.

1. __Independent Updates__ - Updates to the OS don’t interfere with the running application so 
long as docker remains running, and updates to the App don’t interfere with the operating system.

1. __Easy Upgrades__ - For mature applications that have persistent data, the vendor wants you to be 
able to upgrade their application easily with Docker. Update mechanisms and scripts are usually 
all baked into the docker image. You don’t have to worry about crazy upgrade processes. 
Nine times out of ten, you simply stop a running container, and start a newer container that points 
to the same mounted volumes for the application data.

---

### Ansible

<a title="Ansible.com / Public domain" href="https://commons.wikimedia.org/wiki/File:Ansible_logo.svg"><img width="128" alt="Ansible logo" src="https://upload.wikimedia.org/wikipedia/commons/thumb/2/24/Ansible_logo.svg/256px-Ansible_logo.svg.png"></a>

<a href="https://commons.wikimedia.org/wiki/File:Ansible_logo.svg" title="via Wikimedia Commons">Ansible.com</a> / Public domain

This is the **A** in PT**A**. See https://www.ansible.com/

I needed a technology that allows me to configure many systems with easy.
After much research, I settled upon Ansible as my configuration management solution of choice. 
It’s agentless, works on linux and windows, and is yaml based, which is easy to read. 
I can use it to manage the base operating systems that docker would run in, and obviously 
setup the system as a docker host as well.

---

### Terraform

<a title="HashiCorp / MPL 2 (https://www.mozilla.org/en-US/MPL/2.0/)" href="https://commons.wikimedia.org/wiki/File:Terraform_Logo.svg"><img width="256" alt="Terraform Logo" src="https://upload.wikimedia.org/wikipedia/commons/thumb/0/04/Terraform_Logo.svg/512px-Terraform_Logo.svg.png"></a>

<a href="https://commons.wikimedia.org/wiki/File:Terraform_Logo.svg" title="via Wikimedia Commons">HashiCorp</a> / <a href="https://www.mozilla.org/en-US/MPL/2.0/">MPL 2</a>

This is the **T** in P**T**A. See https://www.terraform.io/

All of the systems to manage would be virtual machines, and the private cloud provider that I had experience 
with was of course Vmware, but I also wanted a tool that would work with AWS or other popular cloud providers. 
I eventually settled on Hashicorp’s open source tool Terraform. It was released in 2015, so it’s got a few 
years of use under it’s belt and the vsphere plugin is actively developed and provides easy tie ins to the 
vSphere API without having to know any crazy libraries like pyvmomi or PowerCLI. Also, because the virtual 
machines are configured in code, destruction of what was created by Terraform is a breeze. 
There’s no guesswork involved. 

Another great thing about terraform is that it stores state. 
For example, if your Terraform configuration describes three VMs, and when you run terraform, it sees that 
one is missing or perhaps it’s hostname or static IP is incorrect, Terraform will leave the parts of the 
configuration that are correct alone (i.e. the two present VMs), and only do the things it needs to correct 
the state to match what’s in the configuration (i.e. reclone the third VM or fix its hostname or IP).

---

### Packer

This is the **P** in **P**TA. See https://www.packer.io/

I needed a way to create virtual machine template images. You could argue that doing so by hand is preferable 
since it’s a once and done thing, but in reality it’s never one and done. Anyone who’s tried patching a year 
old Microsoft Windows system with Windows Updates knows how painful doing massive jumps in updates can be. 
Not to mention that in order for the rest of the tech stack to work like vSphere and Ansible, 
preliminary configuration needed to be done. I needed a scripted way to create these templates, and
 ashicorp came to the rescue again with a program that will work with not only vSphere, but also with 
 a vast number of virtual machine hosting providers, like AWS.

---

### Secondary Disk

<a title="Juhele / CC0" href="https://commons.wikimedia.org/wiki/File:Cloud_drive.svg"><img width="256" alt="Cloud drive" src="https://upload.wikimedia.org/wikipedia/commons/thumb/2/2c/Cloud_drive.svg/256px-Cloud_drive.svg.png"></a>

<a href="https://commons.wikimedia.org/wiki/File:Cloud_drive.svg" title="via Wikimedia Commons">Juhele</a> / CC0

The last piece to the dockerized PTA solution is persistent data. I can create a template VM on demand 
with packer, I can clone that template to a specific location and setup the hostname, IP, and other 
particulars with Terraform, and I can configure the operating system to act as a docker host and then 
even start a docker container for the desired application all using Ansible. 

The last piece to the puzzle though, is to allow the administrator the ability to completely de couple 
the operating system from the application. You do that by using a secondary disk to store all of 
the persistent data from the docker application. You can then completely destroy the running VM using 
Terraform leaving the secondary disk, then recreate the VM using Terraform, reattaching the secondary disk, 
and then reconfigure the VM’s operating system as a docker host and reconnect the docker container to the 
files on the secondary drive all using Ansible. This approach completely de couples the application from 
the operating system. In fact, the only thing that ties the operating system to the application is the 
requirement that it be able to run docker. That’s the only thing!

##### ...continue to the next topic [Applications](applications.md).

_Send feedback and comments to [jeremy.cornett@forcepointgov.com](mailto:jeremy.cornett@forcepointgov.com) Forcepoint 2020, BSD-3-Clause_