# PTA Overview

## Table of Contents

1. [Summary](README.md)
1. __[The Need](the_need.md)__
1. [Architecture](architecture.md)
1. [Applications](applications.md)
1. [Benefits](benefits.md)
1. [Initial Setup](initial_setup.md)
1. [Building Blocks](building_blocks.md)
1. [Create Your Hosts](create_your_hosts.md)

## The Need

When a company grows, often initiatives will be started to standardize on toolsets, 
and other initiatives will be started to consolidate duplicate services that the business consumes. 
The aim is to provide these two key things...

* __REUSABILITY__

* __EASE OF MAINTENANCE__ 

In most any corporate environment, the mantra is to "DO MORE WITH LESS". As a lone Configuration Management
Engineer back in 2016, I was drowning in things to do, and I couldn't keep up with my home grown solution
to manage the systems I was tasked with. I needed a better way.

If you examine the average engineering organization, you have a spectrum of how servers are managed.
On the far left you have systems created by Engineers. These may be maintained well, but often they have
many pitfalls. On the far right, you have systems maintained by your IT organization. These have their
pros and cons as well.

### Created by Engineers - Pros

* Exactly What You Wanted - You get systems that meet the needs of your engineers because an engineer is the one who set it up.
The feedback loop between customer and producer/maintainer is ideal, as it's the same person.

### Created by Engineers - Cons

* The Snowflake - The system was setup manually and now a year later, you're asked to maintain it because 
the guy who created it left the company. You're afraid to update it for fear of breaking the application.
Also, the Snowflake is often plagued by improper setups and shortcuts to get things working. After all,
the person who setup the application still has their real day job as a Software Engineer developing applications
that make the company money.

* The Snowflake Tribble - The system was setup once manually, and everyone who wants that application decides to
clone the VM. If you need to change something about all of the VMs like patch a security hole or even
run periodic system updates, it's a challenge because you may or may not know where all of these 
systems are or how to log into them because the passwords were changed.

### Setup and Maintained by Separate Team - Pros

* Four 9's - Often these applicatiosn are maintained with four 9's of uptime, with someone always on Pager Duty
or doing long weekends to upgrade the applications after hours. Lately, systems such as this are 
managed with Kubernetes to provide that uptime.

* Single Application - It can be helpful sometimes if there's only one instance of a particular application.
It is easier to maintain one instead of multiple instances of an application for sure.

### Setup and Maintained by Separate Team - Cons

* Single Application - When there's only one instance of an application, often the team responsible for
the maintenance will disallow customization that the Engineers request because it can make the 
system harder if not impossible to upgrade, 
especially if many teams are requesting their own customizations.

* Second Class Citizen - Sometimes the Engineering systems are treated as second-class citizens, meaning that the SysOps
have many other systems the need to maintain, and one specific to a single team is a low priority
versus one that the entire company uses. In the case of troubleshooting, the SysOp might just
send over root/admin credentials and ask the Engineers to fix it how they need it because the SysAdmin
doesn't have the knowledge of the application or the time to help.

### PTA Pros

PTA is the happy medium between the pros and cons of systems setup by Engineers versus systems maintained by a
separate team. The Cons of the Snowflake and Tribbe are negated because the Engineers responsible for the PTA
systems are imbedded into the Engineering teams, and the Cons of systems Setup and Maintained by Separate Teams
are negated because the Engineers can provide immediate feedback to the PTA admin and the PTA admin can spin up
whatever systems are needed by the Engineers in a reproducible fashion. 

### PTA Cons

I will say PTA isn't designed to give four 9's of uptime. You could possibly work that in fashion,
if you want your PTA admins to work after hours, but I personally don't think it's necessary.

##### ...continue to the next topic [Architecture](architecture.md).

_Send feedback and comments to [jeremy.cornett@forcepointgov.com](mailto:jeremy.cornett@forcepointgov.com) Forcepoint 2020, BSD-3-Clause_
