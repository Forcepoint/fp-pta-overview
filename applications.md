# PTA Overview

## Table of Contents

1. [Summary](README.md)
1. [The Need](the_need.md)
1. [Architecture](architecture.md)
1. __[Applications](applications.md)__
1. [Benefits](benefits.md)
1. [Initial Setup](initial_setup.md)
1. [Building Blocks](building_blocks.md)
1. [Create Your Hosts](create_your_hosts.md)


## Applications

1. Solicit the use of PRTG for monitoring.

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


