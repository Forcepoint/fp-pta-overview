# PTA Overview

## Table of Contents

1. __[Summary](README.md)__
1. [The Need](the_need.md)
1. [Architecture](architecture.md)
1. [Applications](applications.md)
1. [Benefits](benefits.md)
1. [Initial Setup](initial_setup.md)
1. [Building Blocks](building_blocks.md)
1. [Create Your Hosts](create_your_hosts.md)

## Summary

PTA is method for combining the principles of DevOps with Infrastructure as Code to provide
a robust, adaptable, and scalable way to manage applications for your Engineering organization.
The toolsets utilized herein are not trivial. This is not a batteries included solution.
This requires an understanding of the software engineering principles involved 
as well as a solid background in configuration management. 

To implement the PTA solution, I would recommend either a dedicated Engineer in 
the Engineering team who desires PTA, or who is a member of a DevOps team that
is on loan to various Engineering teams as an embedded resource (EX: on their sprint team, 
maybe rotated between other teams depending on the type of work required). This person
would manage the PTA provided applications, as well as provide work and guidance to the 
Engineering team as "the glue" between their PTA infrastructure and the Software Engineers.

This Engineer needs to be...
 
1. A Certified Jenkins Engineer.
1. Fairly familiar with Docker and the benefits containerization provides.
1. Very familiar Configuration Management, Ansible in particular.
1. Understand the benefits of Infrastructure as Code.
1. Versed in Python scripting.
1. Familiar with your cloud hosting provider of choice.
    1. Note that PTA was designed to work with VMware vSphere, but you can use these principles
    with any cloud hosting provider and your own Terraform Modules for it.

As of this writing in the summer of 2020, PTA has been under development and active use for three years.
This is a mature solution that has proven itself, and is now available as an open source solution for
anyone to adopt and use. Using the principles behind PTA will provide great money and time savings. 
Engineering productivity will increase and maintenance costs will drop because of how easy the system 
is to maintain and improve once it's up and running.

When I began this journey in 2016, I desperately wished for a solution such as this. 
I sincerely hope it will help you and your organization.

##### ...continue to the next topic [The Need](the_need.md).

_Send feedback and comments to [jeremy.cornett@forcepointgov.com](mailto:jeremy.cornett@forcepointgov.com) Forcepoint 2020, BSD-3-Clause_