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

---

### Docker

Docker at it’s heart is a means to abstract as much or as little of a running system or application as you want.

![alt text](https://upload.wikimedia.org/wikipedia/commons/7/79/Docker_%28container_engine%29_logo.png "Logo Title Text 1")

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

### Packer



---

### Terraform


---

### Ansible



---

1. Call out the drawbacks of using your own CA instead of a public one.



##### ...continue to the next topic [Applications](applications.md).

_Send feedback and comments to [jeremy.cornett@forcepointgov.com](mailto:jeremy.cornett@forcepointgov.com) Forcepoint 2020, BSD-3-Clause_