# PTA Overview

## Table of Contents

1. [Summary](README.md)
1. [The Need](the_need.md)
1. [Architecture](architecture.md)
1. [Applications](applications.md)
1. [Benefits](benefits.md)
1. __[Initial Setup](initial_setup.md)__
1. [Building Blocks](building_blocks.md)
1. [Create Your Hosts](create_your_hosts.md)

## Initial Setup

If you've decided to adopt PTA or to at least try it out, this is where you should start.

So here's the rub. You have a chicken-and-egg problem out of the gate. The steps here
will setup your PTA Controller Jenkins instance, but it's up to you to decide if you want to
house part or all of the PTA Git repositories (repos for your own hosts, repos for the ansible roles, and 
repos for the terraform modules) in your PTA provided Gitlab instance or elsewhere in your own
Git hosting solution. I opted for the PTA provided Gitlab instance, but that's just me.
If you decide the PTA Gitlab instance, you'll need to modify the following repositories
and keep them locally on your workstation until you get your Gitlab instance up and running.
Once that's done, you should wire up all the needed Jenkins jobs in your PTA Controller.

### Planning and Warning

By now, you've likely got an idea of a couple applications or systems you want to setup. If you intend
to use Gitlab, Jenkins, or Artifactory, those Ansible roles are all configured to set them up with
HTTPS enabled by default. Also, you need to provide DNS names for them. Infact, before you can setup
your own PTA systems, you need to do so with your PTA Controller Jenkins instance, 
which is the end goal of this Initial Setup.

If you don't have the ability to create DNS entries in your organization, I suggest you start that process
to get ones created for your PTA Controller and for your PTA Controller Packer build node, at the very least.
You'll also need static IPs for those systems. Also, you'll need a certificate for the PTA Controller.
I beg you not to use a self-signed certificate. Please obtain one from a Certificate Authority.

If your company runs your own internal CA, I would highly recommend not using it for any of your PTA systems,
or at the very least, not Artifactory if that's possible. Keep in mind that every single developer workstation, 
every single VM, and every single docker container that interacts with one of these PTA provided systems
will have to install the public certificate for your internal CA. Also likely, they'll have to install
it in multiple places for each, as some applications manage their own cert store.
The $1000 spent for your Artifactory wildcard certificate from a public CA that's already trusted
in most operating systems is $1000 very, very, very well spent. It will save your PTA admin and also 
your developers tons of headache.

### Steps

Follow these steps one after the other to get the systems needed for provisioning more systems that
you design.

1. The first step is to create a test template VM.
This is handled via https://github.com/Forcepoint/fp-pta-host-template-centos-7-test
Clone that repository to your workstation and follow the directions in the readme.

1. Next is to designate a "golden" template from that test template VM.
This is handled via https://github.com/Forcepoint/fp-pta-host-template-centos-7-prod
Clone that repository to your workstation and follow the directions in the readme.

1. You now have the templates that you need. You're ready to create your PTA Controller, which is a
Jenkins instance that you use to run jobs for creating your templates and other systems.
This is handled via https://github.com/Forcepoint/fp-pta-host-ptacontroller-master
Clone that repository to your workstation and follow the directions in the readme.

1. You've got your PTA Controller, and you can go on to creating your own hosts. At your earliest
convenience I really recommend setting up a Jenkins node on the PTA Controller that can run Packer
and VMware Workstation for your jobs that create the initial template.
This is handled via https://github.com/Forcepoint/fp-pta-host-ptacontroller-node-01
Clone that repository to your workstation and follow the directions in the readme.

1. Assuming you want to setup Windows 10 VMs via PTA, at some point you should get these setup as well.
    1. https://github.com/Forcepoint/fp-pta-host-template-windows-10-test
    1. https://github.com/Forcepoint/fp-pta-host-template-windows-10-prod

##### ...continue to the next topic [Building Blocks](building_blocks.md).

_Send feedback and comments to [jeremy.cornett@forcepointgov.com](mailto:jeremy.cornett@forcepointgov.com) Forcepoint 2020, BSD-3-Clause_