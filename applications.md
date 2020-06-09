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

You've got the basis for the architecture. What systems would I want to manage with this PTA solution?
The primary use case is what I like to call the CM Trifecta. Your average engineering organization
should be using CI/CD, and I wanted solutions that were free or next to, and ones that could be hosted on-prem
should the be a requirement for your organization, 
and I've found there are three applications that fit the bill best - Jenkins, Gitlab, and Artifactory. This isn't
to say you can't host other things with PTA. You definitely can! For instance, I've put together a monitoring solution
and an automated testing solution.

---

### The Scheduler

<a title="The Jenkins project (http://jenkins-ci.org/) / CC BY-SA (https://creativecommons.org/licenses/by-sa/3.0)" href="https://commons.wikimedia.org/wiki/File:Jenkins_logo_with_title.svg"><img width="256" alt="Jenkins logo with title" src="https://upload.wikimedia.org/wikipedia/commons/thumb/e/e3/Jenkins_logo_with_title.svg/256px-Jenkins_logo_with_title.svg.png"></a>

<a href="https://commons.wikimedia.org/wiki/File:Jenkins_logo_with_title.svg" title="via Wikimedia Commons">The Jenkins project (http://jenkins-ci.org/)</a> / <a href="https://creativecommons.org/licenses/by-sa/3.0">CC BY-SA</a>

There are many CI/CD scheduling solutions out there. The best by far IMHO is __Jenkins__ though.
It's free, widely used, been around for nearly a decade, has a thriving development community,
and of course is provided as a Docker container.

---

### The Source Code Repository

<a title="GitLab B.V. / MIT (http://opensource.org/licenses/mit-license.php)" href="https://commons.wikimedia.org/wiki/File:GitLab_logo.svg"><img width="256" alt="GitLab logo" src="https://upload.wikimedia.org/wikipedia/commons/thumb/e/e1/GitLab_logo.svg/256px-GitLab_logo.svg.png"></a>

<a href="https://commons.wikimedia.org/wiki/File:GitLab_logo.svg" title="via Wikimedia Commons">GitLab B.V.</a> / <a href="http://opensource.org/licenses/mit-license.php">MIT</a>

There are many source code repository solutions out there. The best free solution
that I could install with Docker and that uses Git (the de facto SCM tool these days) is __Gitlab__.

---

### The Artifact Manager

<a title="jfrog / CC BY-SA (https://creativecommons.org/licenses/by-sa/4.0)" href="https://commons.wikimedia.org/wiki/File:Jfrog.png"><img width="64" alt="Jfrog" src="https://upload.wikimedia.org/wikipedia/commons/e/e0/Jfrog.png"></a>

<a href="https://commons.wikimedia.org/wiki/File:Jfrog.png" title="via Wikimedia Commons">jfrog</a> / <a href="https://creativecommons.org/licenses/by-sa/4.0">CC BY-SA</a>

I've never seen a better solution for this than JFrog Artifactory. It's not free,
but it's still worth every penny. I highly recommend you explore this tool for managing your artifacts.
It provides tracability, linking back to your CI/CD and SCM tools, de-duplication, mirror of external sources,
and exposing of artifacts via their native protocols. Need a docker registry? Done. Need a pypi repo? Easy.
Need to mirror ISOs from a public mirror that's slow to download? Sure! Artifactory is the swiss army knife 
for artifact management you wish you'd always had.

---

### Simple Monitoring

<a title="Paessler AG / Copyrighted free use" href="https://commons.wikimedia.org/wiki/File:Prtg-network-monitor-logo.png"><img width="256" alt="Prtg-network-monitor-logo" src="https://upload.wikimedia.org/wikipedia/commons/thumb/6/69/Prtg-network-monitor-logo.png/256px-Prtg-network-monitor-logo.png"></a>

<a href="https://commons.wikimedia.org/wiki/File:Prtg-network-monitor-logo.png" title="via Wikimedia Commons">Paessler AG</a> / Copyrighted free use

I am not a sys admin by training or profession, so take this with a grain of salt. 
Despite multiple attempts, I had a difficult time finding
a monitoring solution that I could setup with PTA principles and that was also easy to use. 
All of the monitoring solutions in use by IT organizations seemed very powerful, but they had an extremely
steep learning curve. I finally settled upon using Paessler's PRTG network monitoring solution. It's very
user friendly, and I only needed to monitor a couple dozen systems with it, so that kept it within
their free use license. The drawback to PRTG is that it's not a docker appliance, and that it runs on Windows.
I found ways to identify all of the application data and back them up, but it's still not the best application
to manage with PTA. Ease of use outweighed my concerns about application management.

---

### Automated UI Testing

<a title="Diego Molina (diemol) / CC BY-SA (https://creativecommons.org/licenses/by-sa/4.0)" href="https://commons.wikimedia.org/wiki/File:Selenium_Logo.png"><img width="128" alt="Selenium Logo" src="https://upload.wikimedia.org/wikipedia/commons/thumb/d/d5/Selenium_Logo.png/128px-Selenium_Logo.png"></a>

<a href="https://commons.wikimedia.org/wiki/File:Selenium_Logo.png" title="via Wikimedia Commons">Diego Molina (diemol)</a> / <a href="https://creativecommons.org/licenses/by-sa/4.0">CC BY-SA</a>

Our Engineering organization needed to run UI tests and they settled upon the Selenium as the tool for the job. 
Spinning up a Selenium Grid for them was definitely inside the realm of possibilities with PTA.


##### ...continue to the next topic [Benefits](benefits.md).

_Send feedback and comments to [jeremy.cornett@forcepointgov.com](mailto:jeremy.cornett@forcepointgov.com) Forcepoint 2020, BSD-3-Clause_