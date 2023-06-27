# Database Queries and Data Storage

This document is meant to help explain the standards and requirements for using databases, to query and store data.

## Queries
For consistency and simplification of software development, ORM's should be used to integrate with the common libaries currently in use within our code base.  More often than not, they simplify the software development process and make the more more modular and re-usable.

In OPSkins and Wax Express Trade (WET), we use the custom written DatabaseObject class as our ORM, and it is directly tied to Class Models.  Example:  Core/User/User.php extends DatabaseObject

The use of Database Driven Models promotes the Data-Driven Applications patterns of thinking.  This leads to tight coupling of the database to models, which accelerates jumpstarting projects, but can lead to long term problems when the database structures change.  This is rare for us and the benefits have outweighed the negatives.

## Custom Queries
Database Administrators (DBA's) should be helping to analyze all queries written (via ORM or Custom) to the databases.  

#### Query Speed
Queries by default should not take over 0.3 (300 milliseconds) to run.  Anything that runs slower than that should be looked into by the team leads, software architect, and/or devops to ensure that it is ok to continue with the query as is.  The team should then determine the solution, be it a new index, or simply making an exception that the slow query is acceptable.

#### Who Watches the Wat.. err, Queries
Due to a lack of DBA's, or a formal data team, it is up to developers to work with their team leads, software architect, and/or devops group to inform people that new database queries are being written and could impact the production environments.  Explains / Explain Analyze should be used on all queries before going to production, or should at least be checked against the production environment to ensure that all indexes or requirements are met.


## Databse (In Software) Repositories Pattern
Models should be the primary source of access to any database or storage facilities.  Any custom / hand written data access (ex: custom sql queries) should go into a repository class.  Additionally, this leads to much easier testability.

If custom work is required to access data that breaks the softwares normal ORM pattern, then a **Repository Pattern** should be used.  Note that this does NOT mean database only.  One of the major benefits of a Repository Pattern is that WHERE the data lives can be abstracted out, be it a local file, S3, NoSQL, or Relational Database.

Examples
- https://dev.to/kylegalbraith/getting-familiar-with-the-awesome-repository-pattern--1ao3
- https://stackoverflow.com/questions/16176990/proper-repository-pattern-design-in-php
- http://shawnmc.cool/the-repository-pattern

#### SIMPLIFIED Example
A class should be created with the word Repository at the end.

**Good Examples**:
- UserRepository
- SalesArchiveRepository
- ItemGenerationRepository

#### Custom SQL Queries
If nothing else, simply putting the CUSTOM SQL into a Repository class will ensure that all custom/complex queries can be easily found and are not littered around the code base.



## Data Storage

When and where should data be stored?

#### Local storage
Local data storage has VERY useful benefits for things like caching, debugging, and quick access capabilities (no networks required).  Additionally, the format the data is stored in in the local file is much easier to control and can dead to optimization, better security, or to fulfil custom needs for your application.

The primary issue with local storage is that often our code base is split across multiple load balanced services and/or networks.  This minimizes some of the benefits of storing things locally that must be shared across all servers.  

Only use local storage if your application understands that those files might not be accessable across multiple servers.

#### In Memory (Redis)
In memory storage can be 1000's of times faster than normal database calls, but can be tricky when determining where the in memory storage is.  In memory storage (in RAM) is limited to a "per server".  Much like the local storage issues able, this can lead to data not being available to all servers.

With the added network latency and software latency of a tool like Redis (or memcached), we can gain the benefits of in memory storage, as well as having a shared network resource that all servers (in a cluster) can use together.  This allows for amazing speed gains, why only being slowed down by network connectivity.

Due to the cost of running high memory servers, it is not recommended to store ALL data in redis.  Instead, we should be using it to store data which might be used by MANY classes/methods in our software base, or for caching.

#### Database
Any long term storage data needed across multiple servers / environments should be stored in a database.  By default we should be using relational databases.  

Information such as audit details, logging, or temporary storage CAN be put into a database, but should be done so with the understanding that it should be removed and stored in long term storage areas such as flatfiles in S3, or in data warehousing centers where we can keep the data for analytics.

By default, we should keep audit, temporary storage, and logging details in databases for no longer than 2 weeks unless there is a specific reason to keep it longer.

#### Long term (s3)
Data that does not change, or at least changes very rarely, is a good candidate for being able to store in something like S3.  Logs and audit records over time can be compressed and stored on S3, but information like case openings, or one time events are great candidates for S3 as well.  Cloudflares cache can be used to store that data on s3 and access it very quickly without putting the overhead burder on our servers and networks to access the data!

#### Data Warehousing

## Security
- KYC / PII
- Encryption / Decryption

## Data Retention Policies
- logs / audits (2 weeks)
- 

GraphSQL

Write through caching patterns