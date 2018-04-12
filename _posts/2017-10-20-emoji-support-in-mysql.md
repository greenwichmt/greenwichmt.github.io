---
layout: post
title: Emoji support when using mysql
date: 2017-10-20 15:03
author: admin
comments: true
categories: [mysql]
tags: [emoji,mysql]
---



Checking our feedback email, i found that lots of overseas users wish to save emoji/emoticon in their profile and design. Yet our apps currently don't support. So i researched how to support emoji.  

## database -something special when using mysql

I was using utf8 encoding in MySQL which I thought (wrongly) could represent all unicode characters.  
The unicodes for emoticons are fully supported by the UTF-8 encoding; however, MySQL’s utf8 does not! To save emoticons to a MySQL database we need to use utf8mb4.  
This is what [the MySQL documentation](https://dev.mysql.com/doc/refman/5.5/en/charset-unicode-utf8mb4.html) has to say about it:  
> The difference between MySQL’s utf8 and utf8mb4 is that the former can only store 3 byte characters whereas the latter can store 4 byte ones. Therefore with utf8 we can only store unicode characters from the Basic Multilingual Plane. Put more simply, utf8 is suitable for characters from the majority of modern languages and some symbols. Emoticon characters exist in the Supplementary Multilingual Plane for which we need to use utf8mb4.  
Luckily, MySQL 5.5.3 (released in early 2010) introduced a new encoding called utf8mb4 which maps to proper UTF-8 and thus fully supports Unicode, including astral symbols.  
The utf8mb4 Character Set (4-Byte UTF-8 Unicode Encoding)  

## Step 1: Create a backup
Create a backup of all the databases on the server you want to upgrade. Safety first!  
## Step 2: Upgrade the MySQL server
Upgrade the MySQL server to v5.5.3+, or ask your server administrator to do it for you.  
## Step 3: Modify databases, tables, and columns
Change the character set and collation properties of the databases, tables, and columns to use `utf8mb4` instead of `utf8`.  

```sql
# For each database:
ALTER DATABASE database_name CHARACTER SET = utf8mb4 COLLATE = utf8mb4_unicode_ci;
# For each table:
ALTER TABLE table_name CONVERT TO CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
# For each column:
ALTER TABLE table_name CHANGE column_name column_name VARCHAR(191) CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```
Since utf8mb4 is fully backwards compatible with utf8, no mojibake or other forms of data loss should occur. (But you have a backup, right?)  

## Step 4: Check the maximum length of columns and index keys
## Step 5: Modify connection, client, and server character sets
```xml
  <connectionStrings>
    <add name="HSMDbEntities"
    connectionString="metadata=res://*/Data.HSMDbModel.csdl|res://*/Data.HSMDbModel.ssdl|res://*/Data.HSMDbModel.msl;provider=Devart.Data.MySql;provider connection string=&quot;server=rm-domain.mysql.rds.aliyuncs.com;user id=*#uid#*;password=*#pwd#*;persist security info=True;database=hsmdb;CharSet=utf8mb4;Pooling=true;Min Pool Size=2;Max Pool Size=30;&quot;" providerName="System.Data.EntityClient"
  xdt:Transform="SetAttributes" xdt:Locator="Match(name)"/>
  </connectionStrings>
```
```sql
show VARIABLES like 'ver%'
SHOW VARIABLES WHERE Variable_name LIKE 'character\_set\_%' OR Variable_name LIKE 'collation%';
====result====
character_set_client	utf8mb4
character_set_connection	utf8mb4
character_set_database	utf8
character_set_filesystem	binary
character_set_results	utf8mb4
character_set_server	utf8
character_set_system	utf8
collation_connection	utf8mb4_general_ci
collation_database	utf8_general_ci
collation_server	utf8_general_ci
```

## Step 6: Repair and optimize all tables
For each table  
REPAIR TABLE table_name;  
OPTIMIZE TABLE table_name;  
$ mysqlcheck -u root -p --auto-repair --optimize --all-databases  


## Reference：

* <https://stackoverflow.com/questions/30074492/what-is-the-difference-between-utf8mb4-and-utf8-charsets-in-mysql>
* <https://stackoverflow.com/questions/39463134/how-to-store-emoji-character-in-my-sql-database>
* <https://andy-carter.com/blog/saving-emoticons-unicode-from-twitter-to-a-mysql-database>
* <https://mathiasbynens.be/notes/mysql-utf8mb4>
* [emoji unicode table](https://apps.timwhitlock.info/emoji/tables/unicode#block-5-uncategorized)



