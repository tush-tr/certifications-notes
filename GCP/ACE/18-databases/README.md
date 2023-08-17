- [Introduction to Databases](#introduction-to-databases)
- [Database fundamentals: Snapshot, standby etc.](#database-fundamentals-snapshot-standby-etc)
  - [Database Snapshots](#database-snapshots)
  - [Transaction logs](#transaction-logs)
  - [Add a standby](#add-a-standby)
- [Database Fundamentals: Availability and durability](#database-fundamentals-availability-and-durability)
  - [Availability](#availability)
  - [Durability](#durability)
  - [Measuring availability and durability](#measuring-availability-and-durability)
    - [Availability](#availability-1)
    - [Durability](#durability-1)
- [DB Fundamentals: Increasing availability and durability](#db-fundamentals-increasing-availability-and-durability)
  - [Increasing availability](#increasing-availability)
  - [Increasing Durability](#increasing-durability)
- [DB Terminology: RTO and RPO](#db-terminology-rto-and-rpo)
  - [Questions - RTO and RPO](#questions---rto-and-rpo)
  - [Achieving RTO and RPO - Failover examples](#achieving-rto-and-rpo---failover-examples)
- [Read Replicas](#read-replicas)
  - [Reporting and Analytics Applications](#reporting-and-analytics-applications)
  - [Read replicas](#read-replicas-1)
- [Data Consistency](#data-consistency)
  - [Strong consistency](#strong-consistency)
  - [Eventual Consistency](#eventual-consistency)
  - [Read-after-write consistency](#read-after-write-consistency)
- [Database Categories](#database-categories)
  - [Choosing database](#choosing-database)
    - [Few factors for choosing db for your use case](#few-factors-for-choosing-db-for-your-use-case)
  - [Relational Databases](#relational-databases)
    - [Use cases](#use-cases)
- [OLTP Relational Databases in GCP(Cloud SQL, Spanner)](#oltp-relational-databases-in-gcpcloud-sql-spanner)
  - [Popular databases](#popular-databases)
  - [Recommended Google Managed services](#recommended-google-managed-services)
    - [Cloud SQL](#cloud-sql)
    - [Cloud Spanner](#cloud-spanner)
- [OLAP Relational Databases in GCP - Big Query](#olap-relational-databases-in-gcp---big-query)
  - [Recommended GCP Managed Service](#recommended-gcp-managed-service)
    - [BigQuery](#bigquery)
  - [OLAP vs OLTP](#olap-vs-oltp)
- [NoSQL Databases in GCP: Firestore,Datastore, BigTable](#nosql-databases-in-gcp-firestoredatastore-bigtable)
  - [Google Managed services for NoSQL](#google-managed-services-for-nosql)
    - [Cloud Firestore(datastore)](#cloud-firestoredatastore)
    - [Cloud BigTable](#cloud-bigtable)
- [In Memory Databases in GCP: Memory store](#in-memory-databases-in-gcp-memory-store)
  - [Recommended GCP Managed Service](#recommended-gcp-managed-service-1)
    - [Memory Store](#memory-store)
  - [Use cases](#use-cases-1)
- [Databases in GCP](#databases-in-gcp)
- [Databases - Scenarios](#databases---scenarios)
    - [A startup with quickly evolving schema(table structure)](#a-startup-with-quickly-evolving-schematable-structure)
    - [Non Relational DB with less storage(10 GB)](#non-relational-db-with-less-storage10-gb)
    - [Transactional Global database with predefined schema needing to process million of transactions per second](#transactional-global-database-with-predefined-schema-needing-to-process-million-of-transactions-per-second)
    - [Transactional local database processing thousands of transactions per second](#transactional-local-database-processing-thousands-of-transactions-per-second)
    - [Cache data (from database) for web application](#cache-data-from-database-for-web-application)
    - [Database for analytics processing of petabytes of data](#database-for-analytics-processing-of-petabytes-of-data)
    - [Database for storing huge volumes stream data from IOT devices](#database-for-storing-huge-volumes-stream-data-from-iot-devices)
    - [Database for storing huge stream of time series data](#database-for-storing-huge-stream-of-time-series-data)

# Introduction to Databases
- Databases provide organized and persistent storage for your data.
- To choose between different database types, we would need to understand:
  - Availability
  - Durability
  - RTO
  - RPO
  - Consistency
  - Transactions etc.

# Database fundamentals: Snapshot, standby etc.
- Imagine a database deployed in a data center in London.
- Challenges:
  1. Database will go down if the data center crashes or the server storage fails
  2. You will lose data if the database crashes
## Database Snapshots
- Let's automate taking copy of the database(take a snapshot) every hour to another data center in London.
- Challenges
  1. Your database will go down if the data center crashes
  2. (Partially solved): You will lose data if the database crashes
     - You can setup database from latest. snapshot. But depending on when failure occurs you can lose up to an hour of data.
  3. (NEW): Database will be slow when you take snapshots.
## Transaction logs
- Let's add transaction logs to database and create a process to copy it over to the second data center.
- challenges:
  1. Your database will go down if the data center crashes
  2. SOLVED: You will lose data if the database crashes
     - You can setup database from latest snapshot and apply transaction logs
  3. Database will slow when you take snapshots.
## Add a standby
- Let's add a standby database in the second data center with replication.
- Challenges
  1. SOLVED: Your database will go down if the data center crashes
    - You can switch to the standby database.
  2. SOLVED: You will lose data if the database crashes.
  3. SOLVED: Database will be slow when you take snapshots.
    - take snapshots from standby.
    - Applications connecting to master will get good performance always.

# Database Fundamentals: Availability and durability
## Availability
- Will I be able to access my data now and when I need it?
- Percentage of time an application provides the operation expected of it.
## Durability
- Will my data be available after 10 or 100 or 1000 years?
## Measuring availability and durability
- Examples of measuring availability and durability
  - 49's - 99.99
  - 119's - 99.999999999
- Typically, an availability of four 9's is considered very good
- Typically, an durability of eleven 9's is considered very good.

### Availability
| Availability   | Downtime(in a month) | Comment                                                  |
|----------------|----------------------|----------------------------------------------------------|
| 99.95%         | 22 minuts            |                                                          |
| 99.99%(4 9's)  | 4 and 1/2 minutes    | Typically online apps aim for 99.99%(4 9's) availability |
| 99.999%(5 9's) | 26 seconds           | Achieving 5 9's availability is tough                    |

### Durability
- What does a durability of 11 9's mean?
  - If you store one million files for ten million years, you would expect to lose one file.
- Why should durability be high?
  - Because we hate losing data
  - Once we lose data, it is gone.

# DB Fundamentals: Increasing availability and durability 
## Increasing availability
- Have multiple stand bys available OR distribute the database.
  - In Multiple zones
  - In Multiple regions
## Increasing Durability
- Multiple copies of data (standbys, snapshots, transaction logs and replicas)
  - in multiple zones
  - in multiple regions
> Replicating data comes with its own challenges.


# DB Terminology: RTO and RPO
- Imagine a financial transaction being lost
- Imagine a trade being lost
- Imagine a stock exchange going down for an hour
- Typically businesses are fine with some downtime but they hate losing data
- Availability and Durability are technical measures
- How do we measure how quickly we can recover from failure?
  - RPO(Recovery Point Objective): Maximum acceptable period of data loss.
  - RTO(Recovery Time Objective): Maximum acceptable downtime
- Achieving minimum RTO and RPO is expensive.
- Trade-off based on the critically of the data.

## Questions - RTO and RPO
- You are running an application in VM instance storing its data on a persistent data storage. You're taking snapshots every 48 hours. If the VM instance crashes, you can manually bring it backup in 45 minutes from the snapshot. What is your RTO and RPO?
  - RTO - 45 minuts
  - RPO - 48 hours

## Achieving RTO and RPO - Failover examples
| Scenario                                                                                      | Solution                                                                                                                               |
|-----------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| Very small data loss(RPO - 1minute) Very small downtime (RTO - 5 minutes)                     | **Hot Standby** - automatically synchronize data. Have a standby ready to pick up load. Use automatic failover from master to standby. |
| Very small data loss (RPO - 1 minute) But I can tolerate some downtime(RTO- 15 minutes)       | **Warm Standby** - Automatically synchronize data Have a standby with minimum infrastructure Scale it up when a failure happens        |
| Data is critical(RPO - 1 minute) but I can tolerate  downtime of a few hours(RTO - few hours) | Create regular data snapshots and transaction logs Create database from snapshots and transaction logs when failure happens            |
| Data can be lost without a problem (for ex: cached data)                                      | Failover to a completely new server                                                                                                    |

# Read Replicas
## Reporting and Analytics Applications
- New reporting and analytics applications are being launched using the same database
  - These applications will ONLY read data
- Within a few days you see that the database performance is impacted.
- How can we fix the problem?
  - **Vertically scale the database** - increase CPU and Memory
  - **Create a database cluster(Distribute the database)** - Typically database clusters are expensive to setup.
  - **Create read replicas** - Run read only applications against read replicas.
## Read replicas
- Add read replica
- Connect reporting and analytics applications to read replica
- Reduces load on the master databases
- Upgrade read replica to master database (supported by some databases)
- Create read replicas in multiple regions
- Take snapshots from read replicas.

# Data Consistency
- How do you ensure that data in multiple database instances (standbys and replicas) is updated simultaneously?
## Strong consistency
- Synchronous replication to all replicas
  - Will be slow if you have multiple replicas or standbys
## Eventual Consistency
- Asynchronous replication
- A Little lag - few seconds - before the change is available in all replicas
  - In the intermediate period, different replicas might return different values
  - Used when scalability is more important than data integrity
  - Examples: Social media posts, tweets
## Read-after-write consistency
- Inserts are immediately available
  - However, updates would have eventual consistency.

# Database Categories
- There are several categories of databases:
  - Relational(OLTP and OLAP)
  - Document
  - Key value
  - Graph
  - In Memory
- Choosing type of databases for your use case is not easy.
## Choosing database
### Few factors for choosing db for your use case
- Do you want a fixed schema?
  - Do you want flexibility in defining and changing your schema?(Schemaless)
- What level of transaction properties do you need?(atomicity and consistency)
- What kind of latency do you want?(seconds, miliseconds or microseconds)
- How many transactions do you expect?(hundreds or thousands or millions of transactions per second)
- How much data will be stored?(MBs, GBS, TBs, or PBs)
- and lot more

## Relational Databases
- This was the only option until a decade back
- Most popular(or unpopular) type of databases.
- Predefined schema with tables and relationships.
- Very strong transactional capabilities.
### Use cases
- OLTP(Online transaction processing)
- OLAP(Online analytics processing)

# OLTP Relational Databases in GCP(Cloud SQL, Spanner)
- Applications where large number of users make large number of small transactions.
  - small data reads, updates, and deletes
- Use cases:
  - Most traditional applications, ERP, CRM,  e-commerce, banking applications.
## Popular databases
- MySQL
- Oracle
- SQL Server
## Recommended Google Managed services
### Cloud SQL
- Supports PostgreSQL, MySQL and SQL Server for regional relational databases(upto a few TBs)
### Cloud Spanner
- Unlimited scale(multiple PBs) and 99.999% availability
- For global applications with horizontal scaling.

# OLAP Relational Databases in GCP - Big Query
- Applications allowing users to analyze petabytes of data
  - Examples:
    - Reporting applications
    - Data ware houses
    - Business intelligence applications
    - Analytics systems
  - Sample Application: Decide insurance premiums analyzing data from last hundres years.
  - Data is consolidated from multiple (transactional) databases.
## Recommended GCP Managed Service
### BigQuery
- Petabyte-scale distributed data ware house.
## OLAP vs OLTP
- OLAP and OLTP use similar data structures
- BUT very different approach in how data is stored.
- OLTP databases use row storage
  - Each table row is stored together
  - Efficient for processing small transactions
- OLAP databases use columnar storage
  - Each table column is stored together
  - High Compression  - Store petabytes of data efficiently
  - Distribute data - one table in multiple cluster nodes
  - Execute single query across multiple nodes - Complex queries can be executed efficiently.

# NoSQL Databases in GCP: Firestore,Datastore, BigTable
- New approach(actually NOT so new!) to building your databases
  - NoSQL = not only SQL
  - Flexible schema
    - Structure data the way your application needs it
    - Let the schema evolve with time
  - Horizontally scale to petabytes of data with millions of TPS.
- NOT a 100% accurate generalization but a great starting point:
  - Typical NoSQL databases trade-off "strong consistency and SQL features" to achieve "scalability and high-performance"
## Google Managed services for NoSQL
### Cloud Firestore(datastore)
- Managed serverless NoSQL document database
- Provides ACID transactions, SQL-like queries, indexes.
  - Designed for transactional mobile and web applications.
- **Firestore**(Next version of Datastore) adds:
  - Strong consistency
  - Mobile and web client libraries
- Recommended for small to medium databases(0 to a few terabytes)
### Cloud BigTable
- Managed,scalable NoSQL wide column database
- Not Severless(need to create instances)
- Recommend for data size >10 terabytes to several petabytes
- Recommended for large analytical and operational workloads
  - NOT recommended for transaction workloads (Does NOT support multi row transactions - supports only single row transactions)


# In Memory Databases in GCP: Memory store
- Retrieving data from memory is much faster than retrieving data from disk.
- In-memory databses like Redis deliver microsecond latency by storing persistent data in memory.
## Recommended GCP Managed Service
### Memory Store
## Use cases
- Caching, session management, gaming leader boards, geospatial applications.

# Databases in GCP
| Database type              | GCP Services                               | Decription                                                                                                                                                                                                                                                                                 |
|----------------------------|--------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Relational OLTP databases  | Cloud SQL, Cloud Spanner                   | Transactional usecases needing predefined schema and very strong transactional capabilities(Row storage) Cloud SQL: MySQL, PostgreSQL, SQL Server DBs. Cloud Spanner: Unlimited scale and 99.999% availability for global applications with horizontal scaling                             |
| Relational OLAP databases  | Big Query                                  | Columnar storage with predefined schema. Datawarehousing & Bigdata workloads                                                                                                                                                                                                               |
| NoSQL Databases            | Cloud Firestore(datastore), Cloud Bigtable | Apps needing quickly evolving structure(schema-less) Cloud Firestore: Serverless transactional document DB supporting mobile and web apps. smal to medium DBs(0 - few TBs) Cloud BigTable: Large databases(10TB - PBs). Streaming(IOT), analytical & operational workloads. NOT Serverless |
| In memory databases/caches | Failover to a completely new server        | Applications needing microsecond responses                                                                                                                                                                                                                                                 |

---

# Databases - Scenarios
### A startup with quickly evolving schema(table structure)
- Cloud Datastore/firestore
### Non Relational DB with less storage(10 GB)
- Cloud Datastore
### Transactional Global database with predefined schema needing to process million of transactions per second
- Cloud Spanner
### Transactional local database processing thousands of transactions per second
- Cloud SQL
### Cache data (from database) for web application
- MemoryStore
### Database for analytics processing of petabytes of data
- BigQuery
### Database for storing huge volumes stream data from IOT devices
- BigTable
### Database for storing huge stream of time series data
- Cloud BigTable