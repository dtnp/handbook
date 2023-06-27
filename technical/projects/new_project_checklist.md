# New Project Checklist

###### *This document was written on january 2, 2018 by Daniel Tisza-Nitsch* 



## Steps to Follow 

1. [Overview](#overview) 
1. [Company Overview](#company-overview)
	- [C-Level](#c-level)
	- [Teams Overview](#team-overview)
	- [HR Overview](#hr)
	- [Security Overview](#security-overview)
1. [Communication](#communication)
	- [Email Address](#email)
	- [Standups](#standups)
	- [Group Chats](#group-chats)
	- [Direct Communication](#im)
1. [My Team](#team)
	- [Agile](#agile)
	- [Team Roles](#team-roles)
	- [Standups](#team-standups)
	- [Showcases](#team-showcases)
1. [Environment](#environment) 
	- [Repositories](#repositories)
	- [Databases](#databases)
	- [Backend Stack](#backend-stack)
	- [Frontend Stack](#frontend-stack)
	- [Containerization](#containerization)
	- [Software](#software)
1. [Code Overview](#code-overview)
	- [Release Cycles](#release-cycles)
		1. Mangement Approval
		2. Date/Times
		3. ​
	- [Levels to Production](#levels)
	- [Database Migrations](#db-migrations)
	- [Static Scans](#static-scans)
	- [Dynamic-scans](#dynamic-scans)
	- [Compliance Overview](#compliance)
	- [Evidence of Test](#test)
	- [CI/CD](#cicd)
	- [Monitoring](#monitoring)
	- [Security Audits](#security)
	- [Performance Testing](#performance-testing)
	- [Uptime](#uptime)

[[Top]](#)
<a name='overview'></a> 
## Overview 
The point of this document is to create an overall checklist of information needed for 1 person to start their new job.

This should be split into many different documents and used as an onboarding tool to get new employees jump started into their new roles within the company.  As I (Daniel Tisza-Nitsch) do not know what work has been done, I am using this for my own personal knowledge gathering purposes


[[Top]](#)
<a name='company-overview'></a> 
----- 
## Company Overview
Who is in the company, what are their goals, what is the point, what are the future aspirations, basic back story, etc.

[[Top]](#)
<a name='c-level'></a>
- C-Level

	|     | Name | Notes |
	| --- | ---- | ----- |
	| CEO |      |       |
	| CFO |      |       |
	| COE |      |       |

	[[Top]](#)
	<a name='team-overview'></a>
- Teams Overview

	|        | Responsibilities | Notes |
	| ------ | ---------------- | ----- |
	| Team 1 |                  |       |
	| Team 2 |                  |       |
	| Team 3 |                  |       |

	[[Top]](#)
	<a name='hr'></a>
- HR Overview
	1. Don't screw up
	1. Don't be an idiot
	1. Don't piss others off
	1. Remember to take toilet breaks

	[[Top]](#)
	<a name='security-overview'></a> 
- Security Overview
	1. Don't break stuff
	1. Don't put post-its on your screen with passwords
	1. Don't save password to public repositories
	1. Don't plug in USB's from supermarket parking lots

[[Top]](#)
<a name='communication'></a> 
----- 
## Communication
<a name='email'></a>

- Email Address
	1. Do we get xxx@opskins.com emails?
	1. What is my official email address?
	1. Can I access my email from any client?

	[[Top]](#)
	<a name='standups'></a>
- Standups
	1. Who
	1. When
	1. Where
	1. How

	[[Top]](#)
	<a name='group-chats'></a>
- Group Chats
	1. Slack?
	1. Rocket chat?
	1. Teams?
	1. IRC?
	
	[[Top]](#)
	<a name='im'></a>
- Direct Communication
	1. IM?
	1. Hangouts?


[[Top]](#)
<a name='team'></a> 
----- 
## My Team

[[Top]](#)
<a name='agile'></a>
- Agile
	1. Scrum?
	1. Kanban?
	1. Pointing?
	1. Grooming?

	[[Top]](#)
	<a name='team-roles'></a>
- Team Roles

	| Role   | Name | Responsibilities | Notes |
	| ---- | ---- | ------------ | ----- |
	| PO | xxx | In charge of overall project ||
	| PM | xxx | In charge of specific team ||
	| Team Lead | xxx | Helps with all tasks and translating information to PO/PM ||
	| Developer | xxx | Write Code ||
	| Developer | xxx | Write Code ||
	| DevOps | xxx | Code, Servers, DBs, Networking, Backflips ||
	| Tester | xxx | Testing all the things ||

	[[Top]](#)
	<a name='team-standups'></a>
- Standups
	1. How often are these scheduled for the team specifically
	1. Chat tools, Voice chat, Video Chat, Mix and match?

	[[Top]](#)
	<a name='team-showcases'></a>
- Showcases
	1. How often are these scheduled for the team specifically
	1. Triggers for showcases (tickets?  time?)
	1. Retrospect?

[[Top]](#)



[[Top]](#)
<a name='environment'></a> 
----- 
## Environment

[[Top]](#)
<a name='repositories'></a>
- Repositories
	1. Git / SVN
	1. Github / Gitlab
	1. Beanstalk?
	1. Branch development only?
	1. Merge requirements

	[[Top]](#)
	<a name='databases'></a>
- Databases
	1. Local only?
	1. Shared?
	1. Migration / DB updates
	1. "Downtime" / Breaking changes

	[[Top]](#)
	<a name='backend-stack'></a>
- Backend Stack
	1. PHP Version?
	1. Node.JS Version
	1. Unix Flavor and version
	1. Database versions

	[[Top]](#)
	<a name='frontend-stack'></a>
- Frontend Stack
	1. JS Frameworks: Custom? JQuery?
	1. CSS Frameworks: ?  SASS? Normalized?
	1. JS Minimum version?
	1. Transpiliers?

	[[Top]](#)
	<a name='containerization'></a>
- Containerization
	1. Docker?
	1. Vagrant
	1. Virtual Box Images?
	1. Snapshot repositories?

	[[Top]](#)
	<a name='software'></a>
- Software
	1. Required Software? (Office 365)
	1. Licensed Software?
	1. Recommended Software?
	1. Online shared tooling? (Travis, beanstalk, jira, etc)

[[Top]](#)




1. [Code Overview](#code-overview)

[[Top]](#)
<a name='release-cycles'></a>
- Release Cycles
	1. Mangement Approval
	1. Recommended Date/Times
	1. ​NEVER Date/Times

	[[Top]](#)
	<a name='levels'></a>
- Levels to Production
	1. Local
	1. Shared / Integration
	1. Testing (performance)
	1. Staging
	1. Production
	1. ?? Hotfix / Patching Paths ??

	[[Top]](#)
	<a name='db-migrations'></a>
- Database Migrations
	1. How to we make DB changes
	1. How do we push DB changes to diff environments
	1. DBA to validate?


	[[Top]](#)
	<a name='static-scans'></a>
- Static Scans
	1. Local testing unit tests first?
	1. Group scans on merges?
	1. Static Analysis?
	1. Cyclomatic Complexities?
	1. Standardization Testing?

	[[Top]](#)
	<a name='dynamic-scans'></a>
- Dynamic-scans
	1. Any of this done by developers?
	1. Documentation of changes for testers?
	1. Procedures for tester to follow?

	[[Top]](#)
	<a name='compliance'></a>
- Compliance Overview
	1. How do we follow compliance and regulatory laws
	1. Do we need to keep track of software used and report new libraries?
	1. EULA / Licensing laws?

	[[Top]](#)
	<a name='test'></a>
- Evidence of Test
	1. Testers give officially approval for local test?
	1. Management / PO / PM writeoff of tests?
	1. Staging Testing?

	[[Top]](#)
	<a name='cicd'></a>
- CI/CD
	1. Are we?
	1. What software
	1. Free online tooling?

	[[Top]](#)
	<a name='monitoring'></a>
- Monitoring
	1. Dashboards
	1. Emergency Alerts
	1. Long running queries
	1. CDN Heartbeats?

	[[Top]](#)
	<a name='security'></a>
- Security Audits
	1. Anyone looking at our code?

	[[Top]](#)
	<a name='performance-testing'></a>
- Performance Testing
	1. We doing small scale class performance tests
	1. Page Testing
	1. Overall site testing
	1. Mobile testing?

	[[Top]](#)
	<a name='uptime'></a>
- Uptime
	1. Do we care?
	1. Breaking changes scheduled at LOW usage times
		- DB Updates/Deletes
		- Server patching
		- Reboots
		- New installs
		- Etc.
	1. Hot patching updating?
	1. Chaos Monkies and Forceful breaks?

[[Top]](#)
