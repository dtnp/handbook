    Latest Versions
    Table Names
    Column Conventions / Standards
        ID Column
        Underscore ID - Foreign Keys
        Underscore AT - Timestamps
        Underscore TIME - Unix Timestamps
    Column Location (Database Dependent)
        Database reserved keyword columns
        Common Default Fields
        Default Ordering Columns
        Exceptions to Default Ordering Columns
        Good Database Table Examples
    Charsets - UTF8
    Columns, Smallest Size, Max Default Size
    Datetime vs Timestamp vs Int
        ISO Standard 8601
        Documentation and Addition Date/Time Functions
        Storage and Size Reasons
        Developer and Code
        Strict Mode (MySQL)
    Null vs Default
        Benefits of NULL:
        Benefits of Default Values (Avoiding NULL)
    Indexes
        When to add an index (BEST GUESS)
        Multi-Column Indexes - When Are They Used

The goal of this is to set up a strict set of guidelines that MUST be followed for any Relational Database Management System (MySQL/PostgreSQL/Sqlite/etc).  A great many benefits can be achieved ranging from optimization, consistency of how data can be found, understanding foreign key relationships, as well as benefiting all parties needed to use the database (read: not just the developers).
Latest Versions

We should be on the latest versions - and we should be upgrading to strict mode. This will allow for much better future proofing the database as we grow. There should be a full effort to update to the latest database version every 2 years minimum. This allows access to built in optimizations, as well as new tooling functionality.  Upgrade also allow security fixes in older non-supported versions of the databases.
Table Names

    All tables must be lowercase with underscores between them.  Be descriptive with the names, but keep it as short as possible.
    Plurals vs Singular - This is a "hot" :flame: topic, but we should use plurals for table names.  Example:  "people" vs "persons", "items" vs "item", "countries" vs "country"
    Present tense vs Past tense.  Used Present test.  Try to avoid past tense table names that use this if possible.  Examples:  "opened_cases" vs "open_cases", "deposited_items" vs "item_deposits", "archived_items" vs "item_archive"
    Do not use abbreviations where possible.  Good: "blockchain_transactions", Bad: "bc_txns"
    Do not prefix databases tables where possible, create a new databases instead (or schemas in postgresql)
    Avoid tables with overly generic "catch-all" names such as "types", "status".  If you want to use those words, prefix with a more specific explanation of what it is, ex: "user_types", "shipping_status"
    Tables that act as a many-one/many-many should be called "maps" and should use the tables names that it connected together and end with the "map" prefix".  Example:  "recipe_item_map"

Good Table Names:

    users
    item_definitions
    user_item_map

Bad Table Names:

    Types - Way too generic, and capitalized first letters
    idm_json - I don't know what "idm" is (item definition map - write out the full name), and no need to put column "types" in the table name (like json)
    fxeE4_users - Use a new database (or schema in postgresql) instead of prepending names like 'fxeE4', using capitalization 'E', plural

Additional details and information

    "Map Tables", "Association Tables", "Group Tables" - AKA: any table that has multiple foreign keys in it together to make a many-to-many/one-to-many relationship should meet two specific standards in the name
        End with "_map"
        Use the FULL names of the joining tables together (if possible)
        Order the table names by what is the most important thing inside of them
        Examples of Good table names:
            students_classes_map - Table 1: students.  Table 2: classes - we care about students the most here
            items_companies_map - Table 1: items.  Table 2: companies - we care about the items the most here
            trade_offer_item_map - Table 1: trade_offers. Table 2: items - we care about trade offers here the most
    Past tense table names should try to be avoided if possible
        "archived_items" is acceptable, but might make more logical sense to call it "items_archive", allowing the table name to be ordered alphabetically with other "item" related tables.

Column Conventions / Standards
ID Column

EVERY table must have a primary key, auto incrementing, id field - specifically named id, lowercase and as some form of integer (smallint, int, bigint, etc).

id does NOT have to increment by 1 every time, but should default to that. A useful strategy for replicating tabling in a multi-master arrangement is to modulus 10 each table.

The following is an example of 3 multi-master tables (allowing for up to 7 more multi-masters if needed):

    Database 1 - users

table: AUTO_INCREMENT by 10, starting with the number 1
Database 2 - users
table: AUTO_INCREMENT by 10, starting with the number 2
Database 3 - users

    table: AUTO_INCREMENT by 10, starting with the number 3

Multi Master ID Example: If an auto incremented id looks like 813192192, the last number is a '2' and therefor we know it was inserted into Database 2.  This allows for additional metrics / reports / analytics to see which tables are hit more often!
Underscore ID - Foreign Keys

Every foreign key to a different table must use the singular table name, underscore, id - all lower case. This lets us know with a simple and quick glance exactly what the column's id is referring to.

Examples of Good foreign keys:

    user_id - This is the Primary Key ID to the table users
    item_defenition_id - This is the Primary Key ID to the table item_definitions
    trade_offer_item_map_id - This is the Primary Key ID to the table trade_offers_items_map

Example that is not a foreign key:

    uid - in many environment, his represents a "unique id". It does not end with underscore id (_id) therefor it does not relate to any other table.  Using this as a "user id" is not a good practice

Underscore AT - Timestamps

All timestamps should end with _at.  If possible, all names should also be past tense by default

Examples of good names:

    created_at
    updated_at
    deleted_at
    last_accessed_at

Examples of bad names:

    create_at - try to use the past tense here
    access_datetime
    last_used_timestamp

Underscore TIME - Unix Timestamps

All integer based timestamps (unix timestamps) should end with _time.  If possible, all names should also be past tense by default

Examples of good names:

    created_time
    last_accessed_time

Column Location (Database Dependent)

Some databases make it easy to move columns around. This actually leads to some interesting benefits, as well as micro optimizations.
Database reserved keyword columns

The following is a list of columns that should exist on most tables. It is extremely important to understand that these columns are reserved for the database:

    id (int-like) - Already stated above.
    deleted_at (timestamp, default null) - "Soft Delete" column. Records should never be deleted from the database. We need to preserve things for reporting and analytics
        This is to represent that a record in the table has been deleted or not
        "Deleted" in this case simply means "Not available to view".  We can tell the users we delete stuff, but we don't, we just hide it from people
        "Undeleting" things that were mistakenly wiped out is as easy as changing the column from a timestamp to null
        If we create "Audit" tables that show EVER change ever made to a record, then we can keep audits around for both "deleted" and "undeleted" records.
    created_at (timestamp, default current_timestamp) - When the record was created - This must exist in ALL database tables.
    modified_at (timestamp, default current_timestamp) - When a record was updated - This should only exist in tables where records can be updated.

Common Default Fields

    title (string) - Most stables should have a tit≤

    field as the primary name for whatever is in that table. Unless you have a more specific name required, you should default to title.
    alias (string) - The alias is a friendly lookup field in the database which is a "slugified" version of the title (all lowercase, only one space between words, Alpha/Numeric/dash/underscores only. Example: "title": "Jumbo Weapon #3" - "alias": "jumbo_weapon_3"
    description (text) - It is very useful to have a description field in many tables. This can be used for internal notes, as well as obvious descriptions of that the row represents. Ex: table: "user_sale_state". "title": "Awaiting Pickup". "alias": "awaiting_pickup". description: "The listing has been created, but the user hasn't sent the item to the bot yet.".

Default Ordering Columns

All columns should be ordered from smallest to biggest. There are specific exceptions to this to make the tables more optimal as well as easier to do quick information gathering on, but the primary way to sort columns (unless there is a very specific reason why to do this otherwise) is to order by size of information in columns. Order should read from left to right:

    boolean
    tinyint, smallint, mediumint, int, bigint
    float, double
    datetime, timestamp
    char(Example: 5), char(Example: 30), char(Example: 66) - try to avoid char's unless very specifically needed. Varchars use less space and can be faster as a result
    varchar(Example: 5), varchar(Example: 30), varchar(Example: 65)
    text, json, xml
    blobs

Micro Optimization - Computers when columns are sorted from smallest to largest, this is actually a micro optimization in the system. When information is read from a file, or column on computers, they read from left to right. If very small values exist first, they often need less hard drive pages to store the data, and can therefor be parsed faster!

Micro Optimization - Human Reading Much like computers, humans generally read left to right. Software often loads left to right, and/or top to bottom. By having very small values grouped together, it is easier to find and read all of those values without having to scroll (left/right, or top/bottom) to find information if a very large description, block, or textarea exists between values! Think of it like an excel/sheet file - if a ton of long text strings exist between columns, then a lot of scrolling is needed to find potentially simple boolean/integer columns.
Exceptions to Default Ordering Columns

There are specific exceptions to the above ordering rule. This is both for speed optimizations and human readability as well.

    id - This should always be the very first column.
    foreign keys - This should always be the next columns. These columns are often going to be indexed and used in joins, so we want them as close to the left as possible for micro optimizations, as well as readability to know that we join often on them.
    deleted_at - (if we are soft deleting) this should be directly next to foreign keys, if the exist as.  This will be used in almost all queries.
    created_at - Keep created and modified as the very last 2 columns. This is for human readability as we often look for these columns for debugging purposes.
    modified_at - Keep created and modified as the very last 2 columns. This is for human readability as we often look for these columns for debugging purposes.

Good Database Table Examples
DROP TABLE IF EXISTS `user_flags`;
CREATE TABLE `user_flags` (
  `id` tinyint(2) unsigned NOT NULL AUTO_INCREMENT,
  `deleted_at` timestamp NULL,
  `alias` varchar(40) DEFAULT NULL,
  `title` varchar(50) NOT NULL DEFAULT '',
  `description` text DEFAULT NULL,
  `created_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
  `updated_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=UTF8;
INSERT INTO `user_flags` (alias, title, description) VALUES
    ('tos', 'Terms of Serivce', 'Has accepted the TOS'),
    ('email_verified', 'Email Verified', 'Email address has been confirmed'),
    ('use_sms_login_codes', 'Use SMS Login Codes', 'Use phone SMS verification instead of emailing.')
;
DROP TABLE IF EXISTS `user_api`;
CREATE TABLE `user_api` (
  `id` tinyint(2) unsigned NOT NULL AUTO_INCREMENT,
  `user_id` int NOT NULL, -- foreign key to `user` table
  `deleted_at` timestamp NULL,
  `requests` mediumint unsigned DEFAULT NULL, -- unknown what this even does.  if it's an access count, mediumint is probably good (16,777,215)
  `flags` int unsigned DEFAULT NULL, -- not a foreign key, but the `user_flag` table still exists as a reference point
  `last_access` int unsigned DEFAULT NULL, -- currently good as int, 4.29 billion.  we are at 1.5 billion as of late 2018
  `api_key` varchar(64) NOT NULL DEFAULT '',
  `api_key_hash` varchar(64) NOT NULL DEFAULT '',
  `ip_whitelist` text DEFAULT NULL, -- this could very well be a json field instead, or might be better suited as a normalized lookup table!
  `created_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
  `updated_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=UTF8
DROP TABLE IF EXISTS `users`;
CREATE TABLE `users` (
  `id` int NOT NULL AUTO_INCREMENT,
  `id64` bigint(20) unsigned NOT NULL DEFAULT 0, -- While not an official foreign key, it is essentially a backup primary key, therefor sits next to the PK
  `deleted_at` timestamp NULL,
  `steam_level` tinyint(2) DEFAULT NULL,
  `last_ip` int DEFAULT NULL, -- 4 bytes of INET_ATON("127.0.0.1") can be stored here
  `flags` int unsigned DEFAULT NULL, -- not a foreign key, but the `user_flag` table still exists as a reference point
  `contract_token` bigint unsigned DEFAULT NULL, -- Generated from the blockchain
  `sms_phone` bigint DEFAULT NULL, -- Phone numbers are not varchar 255.  They should not be stored as string either (that's a presentation issue)
  `last_login` datetime NOT NULL DEFAULT '1000-01-01 00:00:00', -- Store as proper datetime in DB.  Not int.  Can do DB math/conversions much easier
  `ban_expiration` datetime unsigned DEFAULT '1000-01-01 00:00:00', -- Store as proper datetime in DB.  Not int.  Can do DB math/conversions much easier
  -- `create_time` datetime unsigned DEFAULT '1000-01-01 00:00:00', -- Unless they has a very specific purpose, such as a blockchain create time, use the `created` column instead
  `local_trade_token` char(8) DEFAULT NULL,
  `secret` varchar(64) DEFAULT NULL,
  `login_token` varchar(64) DEFAULT NULL, -- This should not be 255, that is too long ?
  `twofa_backup` varchar(64) DEFAULT NULL, -- This should not be 255, that is too long ?
  `contact_email` varchar(128) DEFAULT NULL,
  `contact_email_normalized` varchar(128) DEFAULT NULL,
  `display_name` varchar(255) DEFAULT NULL,
  `display_image_url` text DEFAULT NULL, -- This was set to text in case a weirdly long url is used - edge case issues
  `created_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
  `updated_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  PRIMARY KEY (`id`),
  UNIQUE KEY `id64` (`id64`)
  UNIQUE KEY `contract_token` (`contract_token`),
) ENGINE=InnoDB DEFAULT CHARSET=UTF8
Charsets - UTF8

The DATABASE (not just tables themselves) should have their default value set to UTF8. Virtually all projects these days will touch some form of internationalization.

All tables should be set to UTF8 as well if the default isn't set on the database level.
Columns, Smallest Size, Max Default Size

Columns should always be sized as small as possible. There are a handful of reasons for that.  MySQL Column Side Example:

    Numeric Values (these are for just ONE column):

        Tinyint = 1 byte. 100,000,000 rows in a db = 100 megs.

        Smallint = 2 bytes. 200,000,000 rows in a db = 200 megs.

        Mediumint = 3 bytes. 300,000,000 rows in a db = 300 megs.

        Int = 4 bytes. 400,000,000 rows in a db = 400 megs.

        Bigint = 8 bytes. 400,000,000 rows in a db = 800 megs.

        More hard drive space is used for bigger values

        Bigger values require longer time to backup

        Bigger values require longer time to restore from failure

        Bigger values require longer time to replicate across a network

        Sanitation/Validation - If we size things correctly, there is less chance of bad data being inputted

    Char Values:

        These values should ONLY be used if we have an exact string size that will not change and will always be 100% consistent

        The only reason to use these is as a visual cue that the string doesn't change. Storage/lookup is the same between char an varchars

    Variable Characters (varchars):

        Sanitation/Validation - If we size things correctly, there is less chance of bad data being inputted

        Technically there is no different in SPEED for storing a 5 byte value in a varchar(20) or varchar(150)

        Limiting sizes can force developers to better sanitize information in code

        Smaller strings make for faster joins and indexes

    Date Time:

        See "Datetime vs Timestamp vs Int" below for details

Datetime vs Timestamp vs Int

RDMS's do NOT recommend ever storing integers as timestamps. Timestamps, Dates, Times, and Datetimes are specially optimized in modern RDMS's and allow for special queries to be performed on them. This includes date addition, subtraction, diffs, timezone conversions, and many more features.

Additionally, ints are not human "readable" / "understandable" just by looking at them. Timestamp/Datetimes are human readable and allows for faster analysis of data.
ISO Standard 8601

https://en.wikipedia.org/wiki/ISO_8601

TL:DR - The "human readable" iso-8601 standard for timestamps / datetimes / date / and time are generally as following:

    Date: YYYY-MM-DD  (2018-09-21)
    Time:  HH:MM:SS (14:23:33) - Military Time (0-23), no am/pm.  Also additionally, microseconds can be added
    Datetime:  YYYY-MM-DD HH:MM:SS (2018-09-21 14:23:33).  Sometimes a letter 'T' is inserted between the date and time portions.  Addtional information can be appended like timezone, but the database will handle that automatically.
    Timestamp: YYYY-MM-DD HH:MM:SS (2018-09-21 14:23:33).  Timestamps allow for automated updates.  See "Storage and Size Reasons" below for additional differences between datetime and timestamp.

Documentation and Addition Date/Time Functions

    MySQL Documentation: https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html
    PostgreSQL Documentation: https://www.postgresql.org/docs/current/static/functions-datetime.html

Storage and Size Reasons

In MySQL, timestamps are different from datetimes due to storage size.

    date = 3 bytes.
    time = 3 bytes.
    timestamp = 4 bytes. This is due to the face that is stored as an int (not big int). This time WILL run out in 2038 when int overflows.
    datetime = 8 bytes. This is due to the face that is stored as an bigint. This will NOT run out in 2038.

Developer and Code

Storing an int for optimization of code is a terrible idea. The different in code for PHP between the following code is extremely minor:
echo date('Y-m-d', $timestamp); // $timestamp: 1534683683.  Output: 2018-08-19
echo date('Y-m-d', strtotime($db_datetime)); // $db_datetime: 2018-08-19 13:01:23. Output: 2018-08-19
Strict Mode (MySQL)

PROPER Datetimes should always be used.

Good Default Timestamp/Datetimes:

    1000-01-01 00:00:00
    1900-01-01 00:00:00
    1970-12-31 23:59:59

Bad Default Timestamp/Datetimes:

    0
    0000-00-00 00:00:00
    1970-00-00 00:00:00
    2000-00-00 00:00:00

Null vs Default

This is debated in the DB world. We should use NULLS.

In MySQL 5.5 and below, IS NULL was slower than using default values. That has changed as of version 5.6 and above; - MySQL official documentation claims that it is now OK for optimization: https://dev.mysql.com/doc/refman/5.6/en/is-null-optimization.html
Benefits of NULL:

    Saving on hard drive space. Null requires the least space.
    Bigger values require longer time to backup
    Bigger values require longer time to restore from failure
    Bigger values require longer time to replicate across a network

Benefits of Default Values (Avoiding NULL)

    Not having to search for default values AND "is not null"
        In where clauses
        In Coalesce / group concat / count / min / max / etc - aggregate functions
        In indexes

Indexes

Eventually, with enough traffic, indexes can cause issues with the system by drastically slowing down inserts/updates/deletes. For now, we should use them more often than not.
When to add an index (BEST GUESS)

    Booleans generally don't need them
    Ints of any kind should get them once over about 10,000 rows
    Strings should get them once over avbout 3,000 rows

Multi-Column Indexes - When Are They Used

Generally speaking, indexes in multi-column indexes are used when all columns are used from the index - OR - when columns are used from the left to to right of the mutli-column index.

Examples (Index: xxx.user_id):

    select * from xxx where user_id = 4; - Index Used
    select * from xxx where status = 2; - Index Not Used

Examples (Multi-Column Index: xxx (user_id, status)):

    select * from xxx where user_id = 4 and status = 2; - Index Used
    select from xxx where user_id = 4; - Index Used (left most USED option in multi-column index)
    select from xxx where status = 2; - Index NOT Used -- columns to the left of this in the multi-column index were not used

Examples (Multi-Column Index: xxx (user_id, status, flag)):

    select * from xxx where user_id = 4 and status = 2; - Index Used

    select * from xxx where user_id = 4; - Index Used (left most USED option in multi-column index)

    select * from xxx where status = 2; - Index NOT Used -- columns to the left of this in the multi-column index were not used

    select * from xxx where user_id = 4 and flag = 2; - Index NOT Used - columns to the left of this in the multi-column index were not used. POTENTIAL that user_id MIGHT be used only, but not flag.


