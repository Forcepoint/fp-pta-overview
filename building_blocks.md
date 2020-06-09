# PTA Overview

## Table of Contents

1. [Summary](README.md)
1. [The Need](the_need.md)
1. [Architecture](architecture.md)
1. [Applications](applications.md)
1. [Benefits](benefits.md)
1. [Initial Setup](initial_setup.md)
1. __[Building Blocks](building_blocks.md)__
1. [Create Your Hosts](create_your_hosts.md)

## Building Blocks

The Terraform Modules and the Ansible Roles is really where the magic happens.

---

### Terraform Modules

Here is a list of the different available Terraform modules for use with PTA. There's really only a couple
variants - 1) Linux or Windows, 2) Datastore or Datastore Cluster, and 3) Second Disk or Not.

With the latest release of Terraform I believe these Modules can be combined down into just one,
but I haven't taken the time to figure out how to do it yet.

1. [vsphere-vm-linux-datastore](https://github.com/Forcepoint/fp-pta-terraform-vsphere-vm-linux-datastore)

1. [vsphere-vm-linux-datastore-cluster](https://github.com/Forcepoint/fp-pta-terraform-vsphere-vm-linux-datastore-cluster)

1. [vsphere-vm-linux-datastore-second-disk](https://github.com/Forcepoint/fp-pta-terraform-vsphere-vm-linux-datastore-second-disk)

1. [vsphere-vm-linux-datastore-cluster-second-disk-drsoverride](https://github.com/Forcepoint/fp-pta-terraform-vsphere-vm-linux-datastore-cluster-second-disk-drsoverride)

1. [vsphere-vm-windows-datastore](https://github.com/Forcepoint/fp-pta-terraform-vsphere-vm-windows-datastore)
  
1. [vsphere-vm-windows-datastore-cluster](https://github.com/Forcepoint/fp-pta-terraform-vsphere-vm-windows-datastore-cluster)

1. [vsphere-vm-windows-datastore-second-disk](https://github.com/Forcepoint/fp-pta-terraform-vsphere-vm-windows-datastore-second-disk)

1. [vsphere-vm-windows-datastore-cluster-second-disk-drsoverride](https://github.com/Forcepoint/fp-pta-terraform-vsphere-vm-windows-datastore-cluster-second-disk-drsoverride)

#### Notes

1. For your applications that need a secondary disk, you should take special care to plan for how
much space you'll need. You can always add a bigger disk and copy files over, but that's painful.
Since the disks are all thin anyways, planning that you'll go big eventually will save you some headache.
I suggest that for Artifactory, your _second_disk_size_ be 3000 (GB) at least. For Jenkins and Gitlab, I have found
that the default of 500 GB is more than enough.

---

### Ansible Roles

Here is a comprehensive list of all the available Ansible roles for PTA. Note that some roles
support both CentOS and Windows, but for some the windows based role has been broken out into
it's own role. Whether it's one role or two is really driven by whether or not the variables
for the role can be used with both. If the answer was no, a separate role was created.

1. [backup-local](https://github.com/Forcepoint/fp-pta-ansible-backup-local)

1. [backup-local-win](https://github.com/Forcepoint/fp-pta-ansible-backup-local-win)

1. [change-sshd-port](https://github.com/Forcepoint/fp-pta-ansible-change-sshd-port)

1. [docker-artifactory](https://github.com/Forcepoint/fp-pta-ansible-docker-artifactory)

1. [docker-gitlab](https://github.com/Forcepoint/fp-pta-ansible-docker-gitlab)

1. [docker-host](https://github.com/Forcepoint/fp-pta-ansible-docker-host)

1. [docker-jenkins](https://github.com/Forcepoint/fp-pta-ansible-docker-jenkins)

1. [docker-selenium-grid-hub](https://github.com/Forcepoint/fp-pta-ansible-docker-selenium-grid-hub)

1. [docker-selenium-grid-node-chrome](https://github.com/Forcepoint/fp-pta-ansible-docker-selenium-grid-node-chrome)

1. [extra-disk](https://github.com/Forcepoint/fp-pta-ansible-extra-disk)

1. [extra-disk-win](https://github.com/Forcepoint/fp-pta-ansible-extra-disk-win)

1. [general-config](https://github.com/Forcepoint/fp-pta-ansible-general-config)

1. [gnome](https://github.com/Forcepoint/fp-pta-ansible-gnome)

1. [jenkins-node-jnlp](https://github.com/Forcepoint/fp-pta-ansible-jenkins-node-jnlp)

1. [jenkins-node-jnlp-win](https://github.com/Forcepoint/fp-pta-ansible-jenkins-node-jnlp-win)

1. [jenkins-node-ssh](https://github.com/Forcepoint/fp-pta-ansible-jenkins-node-ssh)

1. [jfrog-cli](https://github.com/Forcepoint/fp-pta-ansible-jfrog-cli)

1. [mount](https://github.com/Forcepoint/fp-pta-ansible-mount)

1. [ntp](https://github.com/Forcepoint/fp-pta-ansible-ntp)

1. [packer](https://github.com/Forcepoint/fp-pta-ansible-packer)

1. [prtg](https://github.com/Forcepoint/fp-pta-ansible-prtg)

1. [terraform](https://github.com/Forcepoint/fp-pta-ansible-terraform)

1. [virtualbox](https://github.com/Forcepoint/fp-pta-ansible-virtualbox)

1. [vmware-workstation](https://github.com/Forcepoint/fp-pta-ansible-vmware-workstation)

##### ...continue to the next topic [Create Your Hosts](create_your_hosts.md).

_Send feedback and comments to [jeremy.cornett@forcepointgov.com](mailto:jeremy.cornett@forcepointgov.com) Forcepoint 2020, BSD-3-Clause_
