---
layout: post
title:  "Reset database state for acceptance and integration tests with cloud databases"
date:   2019-06-25 10:58:00 +1100
categories: test cloud database
---
A robust test suite is essential in the production and maintenance of quality software. One of the problems people face while developing tests is reverting the data state between tests. This becomes even more difficult if tests are run against cloud databases.

Let's start with an overview of the traditional approach: revert the data affected by test after test has finished running. At first glance, this looks just fine. A test will run and at the end, it will remove or insert the data that has been affected. But this doesn't work very well for several reasons:

* Writing tests will take longer and their maintenance will be more expensive. A person writing the tests would have to know database structure and write very specific code for the data reset.
* If clean-up fails it can cause cascading test failures.
* Removing or inserting data in document dbs such as Cosmos DB might result in throttling of requests and overall degradation of the test suite execution speed.
* Changes to database structure would require significant rework in test clean-up code.

Another approach to database state management is to restore the database to a snapshot. This work really well, it is fast and mostly trouble free. But you can't do snapshot restore against if your database is software as a service (SaaS) and this also not going to work for the document databases.

So what's the alternative. The alternative is actually very simple you just need to reset `all` data affected by tests. The reset would happen before every test. This sounds great but how are we going to produce and maintain code responsible for the data reset. To answer that let's take a look at how software is usually developed. If software uses a database it will need to initialize the data. There are different approaches to data initialization and migration but I am going to simplify it for the sake of brevity. So we have established that the software already has some code that initializes the database. The next step is to split the initialization into two parts: `constant` and `changeable` (by tests). As software is written any changes to database structure and/or standing data affecting `changeable` part of database state would need to be packaged and shared with the test suite. This could be as simple as sql script but shared module or program will work too.

This is all nice and well but it doesn't cover document databases. For the document database, the situation is usually a bit more complicated and would potentially require design changes. But the general rule that I have used in the past is to simply drop and recreate `changeable` collections. This works well and usually recreating collection costs a lot fewer throughput units.

I have used the described approach for Azure SQL database and Cosmos DB. But this should work for any other relational or document database in the cloud.

As a conclusion, I want to urge people to adopt the approach of resetting all `changeable` data and not to worry about finding what changed and only reverse that particular part of the data. This will make your test suite more stable its development faster and maintenance easier.
