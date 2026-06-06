---
title: "Database Architecture"
---
# Database Architecture

In the database management systems, there are several components that makes a query action happened. In this document, I'm going to understand whats are these components, understand them basically, and know their responsibility.

**As a resource, I used this [youtube crach course](https://youtu.be/pPqazMTzNOM?si=3gQJ74kIuLX66HUQ)** and I recommend you for better understanding, watch this crash course by reading my notes.

## Components

I'm going to separate the components with a breif explaination and responsibility one by one:

### Client

Client is a person or an application who want to call a DBMS for executing a query and expect the result.
Client to have interaction with DB, need a way, and often this will happens using a **network layer** that is explained in next section

### Network layer

In most modern databases like `PostgreSQL, MySQL,...` there is a network layer that handles client requests and pass them to management system.
**Imagine this layer like a middleware between Database and Client.**

> [!NOTE]
> `SQLITE` which is the simplest database, is designed embedded, which means it handles in a single process, and doesn't have a **network layer** at all.

![Client and network layers](/images/database-architecture/client-and-network.png)

### FrontEnd component

For know what exactly client is asking for a database, the database need to understand the query instructions step by step, and this would be handled in **frontend layer**. 

Queries like `SELECT * FROM table`, `CREATE TABLE ...`, needs to validate at first, and they will have a validation, so:

#### Tokenizer

This component will understand and validate command or query words step by step.
The **Tokenizer** is responsible for tokenizing the query, basically devide the query into simple words and tokens that are understandable for rest of the components. Tokenizer also check and validate the query by checking is the token wrote correctly or not. For example `FROM table SELECT *` is not valid but `SELECT * FROM table` is a valid token.

#### Parser

If the query was valid, then is turn for **parser** to tell the system which steps need to be performed for executing this query.

**The parser will check the logic of the query is write or not, while the tokenizer will separate each words into tokens to check they are valid tokens or not.**

#### Optimizer

**Optimizer** is where the database find the best solution for executing a query. The optimizer will find a way or ways for responding the query, then choose the best one and pass it to the rest components to be executed and get process by them.

For example, optimizer will check if it needs to full scan, or use index for fetching a result.

![Front end layer](/images/database-architecture/frontend-layer.png)

### Execution Engine

Execution engine is where the query is going to be executed, and operations are need, are managed by this engine component.

Execution engine have this responsibilities:

1. Query Execution
2. Cache manager: For caching the queries that are executed mostly again and again
3. Utility service: For authentication, security or even backups we need some utility that are stored in a unit named **Utility service**

![Query Execution engine](/images/database-architecture/query-executor.png)

> [!IMPORTANT]
> In databases, implementing the TRANSACTION concepts is not actually as easy as it sounds, it is a complicated operation that is going to be handled by DBMS.

### Transaction manager

Transaction manager is where the database is going to handle the transaction operations.

### Lock management

Lock management is going to work closely with transaction manager to handle the lock on concurrent operations to guarantee the transaction concepts.
There are different ways to handle lock, some databases lock just a single row, some of them lock the entire database system, and it has multiple way for implementation.

### Recovery manager

If during an operation, an error occure or database crash happen, then after disaster we want to recover the database system to have transactions. For doing this, we have a component for recovery after problems, named **recovery manager**.

![Transaction manager](/images/database-architecture/transaction-manager.png)
