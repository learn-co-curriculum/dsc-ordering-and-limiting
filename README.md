# Basic SQL Queries

## Objectives

1. What are SQL queries and why learn them?
2. Learn some of the basic queries and how to construct them

## What is a SQL Query?

The term "query" refers to any SQL statement that retrieve data from your database. In fact, we've already written a number of SQL queries using basic `SELECT` statements. So, we've already seen how to retrieve single units of data, or rows, with queries like these: 

To select all of the rows from a `cats` table:

```sql
SELECT * FROM cats;
```

To select only rows representing data meeting certain conditions: 

```sql
SELECT * FROM cats WHERE name = "Maru";
```

What if, however, we wanted to select the oldest cat? Or all of the cats that don't currently belong to an owner. Or all of the cats with short names? 

Data storage isn't very useful if we can't manipulate, view and analyze that data. Luckily for us, SQL is actually a powerful tool for doing just that. 

In this exercise, we'll walk through executing a handful of common and handy SQL queries. If you want to take a deeper dive, however, feel free to check out the resources at the bottom of the page. 

## Code Along: SQL Queries

### Preparing our Database

Once again we'll be using the `pets_database.db` from the previous exercises. Navigate to the directory that holds that database and connect to it with `sqlite3 pets_database.db`. 

Let's add a few more cats to our `cats` table to make this more interesting: 

```sql
sqlite> INSERT INTO cats (name, age, breed, owner_id) VALUES ("Hana", 1 , "Tabby", 1);
sqlite> INSERT INTO cats (name, age, breed) VALUES ("Lil' Bub", 5, "American Shorthair");
sqlite> INSERT INTO cats (name, age, breed) VALUES ("Moe", 10, "Tabby");
sqlite> INSERT INTO cats (name, age, breed) VALUES ("Patches", 2, "Calico");
```

Let's checkout our `cats` table now: 

```sql
sqlite> SELECT * FROM cats;
```

This should return: 

```bash
1|Maru|3|Scottish Fold|1
2|Hana|1|Tabby|1
3|Lil' Bub|5|American Shorthair|
4|Moe|10|Tabby|
5|Patches|2|Calico|
```

**Top-Tip:** You can format the output of your select statements with a few helpful options: 

```sql
.header on       # output the name of each column
.width auto      # adjusts and normalizes column width
# or
.width NUM1, NUM2 # customize column width
```

Run the first two commands and then execute the above `SELECT` statement instead and you should see something like this: 

```bash
id  name        age    breed          owner_id  
--  ----------  -----  -------------  ----------
1   Maru        3      Scottish Fold  1         
2   Hana        1      Tabby          1         
3   Lil' Bub    5      American Shor            
4   Moe         10     Tabby                    
5   Patches     2      Calico     
```

Much better. 


### `ORDER BY`

The first query modifier we'll explore is `ORDER BY`. This modifier allows us to order the table rows returned by a certain `SELECT` statement. Here's a boilerplate `SELECT` statement that uses `ORDER BY`:

```sql
SELECT column_name FROM table_name ORDER BY column_name ASC|DESC;
```

Let's select our cats and order them by age: 

```sql
sqlite> SELECT * FROM cats ORDER BY age;
```

This should return the following: 

```bash
id  name        age    breed       owner_id  
--  ----------  -----  ----------  ----------
2   Hana        1      Tabby       1         
5   Patches     2      Calico                
1   Maru        3      Scottish F  1         
3   Lil' Bub    5      American S            
4   Moe         10     Tabby  
```
When using `ORDER BY`, the default is to order in ascending order. If we want to specify though, we can use `ASC` or "ascending" or `DESC` for "descending". Let's try to select all of our cats and sort them by age in descending order. 

```sql
sqlite> SELECT * FROM cats ORDER BY age DESC;
```

This should return

```bash
id  name        age    breed       owner_id  
--  ----------  -----  ----------  ----------
4   Moe         10     Tabby                 
3   Lil' Bub    5      American S            
1   Maru        3      Scottish F  1         
5   Patches     2      Calico                
2   Hana        1      Tabby       1 
```   

### `LIMIT`

What if we want the oldest cat? If we want to select extremes from a database table––for example, the employee with the highest paycheck or the patient with the most recent appointment––we can use `ORDER BY` in conjunction with `LIMIT`. 

`LIMIT` is used to determine the number of records you want to return from a dataset. For example: 

```sql
SELECT * FROM cats ORDER BY age DESC LIMIT 1;
```

This part of the statement: `SELECT * FROM cats ORDER BY age DESC LIMIT 1;` returns all of the cats in order from oldest to youngest. Setting a `LIMIT` of `1` returns just the first, i.e. oldest, cat on the list. 

Execute the above statement in your terminal and you should see: 

```bash
id  name        age    breed       owner_id  
--  ----------  -----  ----------  ----------
4   Moe         10     Tabby                 
```
Let's get the two oldest cats: 

```sql
SELECT * FROM cats ORDER BY age DESC LIMIT 2;
```

Execute that statement and you should see: 

```bash
id  name        age    breed       owner_id  
--  ----------  -----  ----------  ----------
4   Moe         10     Tabby                 
3   Lil' Bub    5      American S            
```

### `COUNT`

Lastly, we'll talk about one SQL aggregate function, `COUNT`. 

**SQL aggregate functions** are SQL statements that retrieve minimum and maximum values from a column, sum values in a column, get the average of a column's values, or count a number of records that meet a certain conditions. You can learn more about the these SQL aggregators [here](http://www.sqlclauses.com/sql+aggregate+functions) and [here](http://zetcode.com/db/sqlite/select/). 

For now, we'll just focus on `COUNT`. `COUNT` will count the number of records that meet a certain condition. Here's a standard SQL query using `COUNT`:

```sql
 "SELECT COUNT([column name]) FROM [table name] WHERE [column name] = [value]"
```
Let's try it out and count the number of cats who have an `owner_id` of `1`:

```sql
SELECT COUNT(owner_id) FROM cats WHERE owner_id = 1;
```
This should return:

```bash
COUNT(owner_id)
---------------
2
``` 