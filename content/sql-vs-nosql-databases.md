---
title: "SQL databases vs NoSQL Databases"
---
# SQL databases vs NoSQL Databases

## Sources:

| Title | Link |
| --- | --- |
| What is SQL Database? - Microsoft | [Link](https://azure.microsoft.com/en-us/resources/cloud-computing-dictionary/what-is-sql-database) |
| Understanding SQL vs NoSQL Databases | [Link](https://www.mongodb.com/resources/basics/databases/nosql-explained/nosql-vs-sql) |


### What is SQL?

SQL, which stands for Structured Query Language, is a domain-specific programming language (e.g., a language targeted to a specific task or problem) that is commonly used for tasks such as inserting, updating, querying, and deleting data within a database. SQL is also used to create and modify database schemas (e.g., data formatting rules, table/index structure ) as well as define database access and administration parameters.


### What is structured data?

Structured data is data that is organized in a consistent, predefined format. Structured data is typically stored in a tabular format, with rows representing individual records and columns representing individual data fields. Structured data is easy to search, query, and analyze, making it ideal for use in relational databases.
Examples include financial transactions, inventory records, or customer lists which are often stored in SQL databases.

### What is SQL Databases?

SQL databases, also known as relational databases, are systems that store collections of tables and organize structured sets of data in a tabular columns-and-rows format, similar to that of a spreadsheet. The databases are built using structured query language (SQL), the query language that not only makes up all relational databases and relational database management systems (RDBMS), but also enables them to “talk to each other”. 

> [!IMPORTANT]
> When the term "SQL database" is used, it refers to a type of database where SQL is the primary programming language used to create and manage that database. SQL application programming interfaces (APIs) contain groups of functions that enable developers to execute and manage database operations without having to create individual SQL commands over and over.

### Relational Databases

Relational databases, or relational database management systems (RDBMSs), store data within rows and columns which are used to form tables. A relationship between the two tables (or more) can be created using a foreign key. These foreign keys (e.g., unique identifiers) maintain predefined relationships that exist between the tables.

> [!IMPORTANT]
> **It's important to note that relational databases are created and managed using a fixed schema.** A fixed schema means that all data ingested into the database must be precisely aligned to predefined formatting standards which limits the types of data structures that relational databases can store. For example, relational databases are not able to process unstructured data (e.g., information that is inconsistent in format and isn't aligned to a preset data model) but are excellent at supporting transactional or financial information that includes structured data or semi-structured types of data (e.g., data that has a consistent format and aligns to a preset data model).

### Examples of different SQL databases:

1. Oracle DB
2. PostgreSQL
3. MySQL
4. SQLite
5. Microsoft SQL Server: **MSSQL**

## Not only Structured Query Language - NoSQL

### What is NoSQL? 

NoSQL, which stands for Not only SQL, is a database management system approach used to ingest, store, and retrieve unstructured data and semi-structured data within a database.
The reason it is called NoSQL is to emphasize that these databases can handle non-tabular, non-relational data models as well as support SQL-like query languages.

### What is unstructured data?

Unstructured data is data that doesn't have a predefined data model or consistent organization.
In addition, unstructured data, such as social media posts, can update and change rapidly while structured data, such as bank transactions, have a much lower rate of change. Examples of unstructured data include pictures, audio files, videos, and maps.

### What is NoSQL database?

NoSQL databases are databases that utilize a flexible schema that accommodates unstructured data and semi-structured data while also utilizing a non-tabular data storage method.

The use of a flexible schema enables NoSQL databases to ingest unstructured data in its native format (e.g., .txt, .JPG, MP3), which is not possible with SQL databases due to the requirement that **all data align to a predefined format**. Further, when NoSQL databases store data, flexible data models are employed so that unstructured data files **can have different data structures** and still be stored within the same collection.

### Types of NoSQL Databases

1. Document Databases
2. Key-Value Databases
3. Column-family Stores
4. Graph Databases
5. Time-Series databases

#### 1. Document Databases

Document databases, sometimes referred to as **object-oriented databases**, store data in documents similar to JSON (JavaScript Object Notation) objects, although they're not JSON stores. They use the drivers returned from native objects to the programming language used by the developer without needing an object relational mapper (ORM). Each document itself is treated as a record and can contain values including numbers, arrays, objects, strings, or even Boolean characters. In addition, key-value pairs, nested documents, or other structured data can be included.

**Popular Databases:** MongoDB

#### 2. Key-Value Databases

Key-value databases collect, retrieve, and store data as groupings of key-value pairs. This means that each data record is represented by a unique key and an associated value.
The key is used to retrieve the corresponding value from the database. 

**Popular Databases:** AWS, ScyllaDB

#### 3. Column-Family Databases

Column-family databases organize data into columns rather than rows, which is helpful when working with wide datasets that are sparse in depth. In fact, column-family stores are sometimes referred to as "wide-column stores." In column-family stores, each row has a different set of columns, with columns then gathered into "families."
These databases are well when you're working with a large-scale datasets that benefits from horizontal scaling to optimize performance.

**Popular Databases:** Apache Cassandra, Hbase

#### 4. Graph databases

Graph databases store data in nodes and edges.
Nodes typically stores data about people, places and things, while edges store information about the relationship between nodes.
Graph databases are useful for querying graph datasets, including social networks.

**Popular Databases:** Neo4j, AWS, Kibana

