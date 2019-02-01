---
title: "SQL #1"
date: 2019-02-01
tags: []
excerpt: ":wrench:"
mathjax: true
---

# Data Modelling  

### Entities & Attributes
An **entity** in database (DB) theory is described as just 'a thing'. Things can have **attributes** and it's attributes which constitute data. An entity can also have a relationship with another entity and share similar data. 
With attributes and known relationships we can access knowledge about the thing and find insights.  

Attributes and relationships have qualities of their own and can be categorized by 3 measures.
1. Cardinality
- Measure of scalability of an entity relationship
- Applies seperately to either direction of relationship
- [A tweet has 1 author but an author may have many tweets]
2. Scalability
- Whether an attribute may hold multiple correct values
- Scalar:[Age, Weight], Non-Scalar:[Email, Address]
3. Determinism
- Whether an attribute value changes or is constant
- Deterministic:[D.O.B., Name], Non-Deterministic:[Time, Age]


### Entity Relationship Diagrams
An ERD shows the entities, attributes, and relationships in a database. Entities are represented by :white_large_square: shapes, attributes by :white_circle: and relationships by :large_blue_diamond:.  

If an attribute can hold multiple values (Non-Scalar) it should have 2 rings around it. The cardinality of a relationship is displayed by a symbol on the connecting line.  

<figure style="width: 60%" class="align-right">
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/4-erd.png" alt="Entity Relationship Diagram">
</figure>  

1: [|]  
0:[o]  
Many: [<]  
1 to many: [|<]  
0 to many: [o<]   

---
# Databases
### RDBMS
Relationship DataBase Management Systems are very common structures for databases and are what SQL is based on. The most popular RDBMS's currently are **Oracle(PL/SQL)**, **MySQL(SQL/PSM)**, and **Microsoft SQL(T-SQL)**.

#### CRUD
The basic transactions that must be available on a database are: **CREATE**, **UPDATE**, **READ**, and **DELETE**. These are the requirements of a NoSQL system, it does not require the ACID requirements as it has the ability to do *Not Only* SQL.

#### ACID
The additional requirements of an RDBM are: **ATOMICITY**, **CONSISTENCY**, **ISOLATION**, and **DURABILITY**.  
Atomicity means transactions are indivisible so always either fully complete for fully fail. Consistency ensures that any written data is valid by all rules of that database. Isolation states a row must be isolated and frozen while being edited. Durability ensures that data is not loss if, for example, power to the database is lost.

#### 3NF (OLTP)
An RDBMS can contain tables of the type 3NF or dimensional or a mixture of the two. 3NF stands for 3rd Normal Form and is the third in a set of table types, each with their own conditions and reliant on the previous.  
**1NF**:
- Each row has a unique key
- Each field has one value
- No repeating groups  
**2NF**:  
- Be 1NF compliant
- Every listed attribute is wholly dependent on the primary key
**3NF**:  
- Be 2NF compliant
- Only store *original* data, nothing that could be calculated

3NF tables come under the umbrella of **On-Line Transaction Processing**. Because of the simplicity of each table the system is filled with many small tables. These are difficult to query as many tables may need to be consulted to get a figure. Howver they are easy to edit as the locking only occurs on a small area.  
3NF OLTP systems are used in transaction-heavy areas such as banking where edits are needed very quickly and queries are less important.  

#### Dimensional (OLAP)
A dimentional table combines the many tables of 3NF into a few very wide tables. This makes them much simpler to query and only 1 or tables may need to be consulted. However editing dimensional tables can cause problems due to the amount of fields that will be locked when a single row is edited.  
Dimensional tables are used when lots of queries are made, primarily in analytics, part of **On-Line Analytical Processing**.

### Operation
All databases have at least 2 files; a **Transaction Log (TL)** and a **Masked Data File (MDF)**. The TL is a log of all actions taken on the database, basically the database metadata. The MDF is the actual data file. Both files are stored on hard memory and are updated from the more immediate RAM memory.  

When a user makes an edit it is stored in the RAM, then transferred to the TL. When a user then queries the DB it checks the RAM first for the change, before going to the final MDF to check. The MDF is updated with actions from the TL after a set time (60s on MS SQL). This time is short enough that no actions are missed by the query as the RAM will store it for over 60s.  

---
# SQL
SQL is a powerful language and engine used for a vast array of data related functions. Strict SQL systems can be distinguished from NoSQL systems which are 'Not Only' SQL but include some extra ability. However, almost all database systems are built on the logic and assumptions of SQL.  

### SQL Server
SQL servers are computers configured to deliver data in the way descibed above. A computer may be especially configured to be an SQL server, but server software may also be installed on a normal PC. The following details relate to a Microsoft Windows install of MS SQL. 

The default MSSQL port is 1433. The commands `net stop <service>` and `net start <service>` can be used to control the server where `<service>` is the service name (`mssqlserver`).  

To access the server as a client a user must use *client tools*. The Microsoft client tool is SQL Server Management Service (SSMS) but there are many alternatives. SSMS uses T-SQL, the MSSQL language variant.  

The **default tables** included in an SQL server are;
- `master`: Central database, contains user info
- `MSDB`: Automated system jobs
- `model`: Template database
- `tempdb`: Stores temporary tables, shared by all users  

**Keys** are unique identifiers for users or instances of an entity. There could be multiple *candidate keys* from which one *primary key* is chosen. Keys can also either be readable *business keys* or indexed *surrogate keys*. The choice of key is mostly up to the user.