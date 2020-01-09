---
layout: post
title:  "Cross subscription copy of Azure SQL database using Transact-SQL"
date:   2020-01-20 10:58:00 +1100
categories: azure cloud sql
---
Copying Azure SQL database cross subscriptions can look daunting at first but there is an easy way to accomplish this. And the best part is that you can do all of it from within the SQL Management Studio.

Follow these steps to copy the database:
To copy database in Azure in the same or different subscriptions perform the following steps:

* Connect to source server master database using server administrator credentials

```sql
CREATE LOGIN cpyusr WITH PASSWORD = 'ReplaceWithNewPassword';
CREATE USER cpyusr FROM LOGIN cpyusr;
```

* Connect to source database using server administrator credentials

```sql
CREATE USER cpyusr FROM LOGIN cpyusr;
ALTER ROLE db_owner ADD MEMBER cpyusr;
```

* Connect to destination server master database using server administrator credentials
  
```sql
CREATE LOGIN cpyusr WITH PASSWORD = 'ReplaceWithNewPassword';
CREATE USER cpyusr FROM LOGIN cpyusr;
ALTER ROLE dbmanager ADD MEMBER cpyusr;
```

* Connect to destination server master database using cpyusr
  
```sql
CREATE DATABASE destinationdb AS COPY OF sourceserver.sourcedb (EDITION = 'Standard', SERVICE_OBJECTIVE = 'S1' );
```

Depending on the database size copying might take a while. During copying the replication link between the source and destination databases will be created. Once copying is finished the replication link will be removed. More details can be found on [Microsoft](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-copy?tabs=azure-powershell#copy-a-database-by-using-transact-sql) site.
