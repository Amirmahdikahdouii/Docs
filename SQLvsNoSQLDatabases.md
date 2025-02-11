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
> It's important to note that relational databases are created and managed using a fixed schema. A fixed schema means that all data ingested into the database must be precisely aligned to predefined formatting standards which limits the types of data structures that relational databases can store. For example, relational databases are not able to process unstructured data (e.g., information that is inconsistent in format and isn't aligned to a preset data model) but are excellent at supporting transactional or financial information that includes structured data or semi-structured types of data (e.g., data that has a consistent format and aligns to a preset data model).

