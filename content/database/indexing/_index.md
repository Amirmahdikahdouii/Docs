---
title: "Database Indexing"
---
# Database Indexing

Indexing in databases is a way for optimizing retrieve data from table or collections.
It improves the query performance by **reducing the I/O operations**, thus reducing the time for locate and access data.
It's a **Data Structure** that provides faster data access. Most of indexing strategies usually consists of 2 column: First the key that you want to search it in data, and second the pointer that refers the block that data resides.
This proccess will reduce the disk access requied to fulfill a query.

Without indexing, the database engine should check every possible record to see if its match to your condition for returning it or not.

## How indexing works?

Think of a database index like a book index (Usually stored at the end of the book). Each keyword is stored in an alphabetical order so the user could search and find the word it's looking for easily, without searching all of the words.

For example, here for finding the word `MongoDB` we can have this strategy:

![Database indexing work](/images/database-indexing/how-indexing-works.png)

## How do I choose my proper field to be indexed?

To decide what fields are proper to be indexed, think about the fields that are most likely to used in queries, for example **name**, **ID** or **combination of both**, based on your need.
