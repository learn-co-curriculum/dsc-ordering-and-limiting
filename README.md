# Filtering and Ordering

## Introduction

One of the most powerful aspects of SQL is the ability to include only relevant data in your query results. In this lesson, you'll learn some additional techniques for limiting the results of your SQL queries using the `WHERE` clause as well as the `ORDER BY` clause combined with the `LIMIT` clause.

## Objectives
You will be able to:
- Write SQL queries to filter and order results
- Order the results of your queries by using `ORDER BY` (`ASC` & `DESC`)
- Limit the number of records returned by a query using `LIMIT`
- Filter results using `BETWEEN` and `IS NULL`

## The Data

For this lesson we'll be using a database called `pets_database.db` containing a table called `cats`.

The `cats` table is populated with the following data:


|id  |name     |age    |breed              |owner_id   |
|----|---------|-------|-------------------|-----------|
|1   |Maru     |3.0    |Scottish Fold      |1.0        |
|2   |Hana     |1.0    |Tabby              |1.0        |
|3   |Lil' Bub |5.0    |American Shorthair |NaN        |
|4   |Moe      |10.0   |Tabby              |NaN        |
|5   |Patches  |2.0    |Calico             |NaN        |
|6   |None     |NaN    |Tabby              |NaN        |

In the cell below, we connect to the database and select all of the records:


```python
import pandas as pd
import sqlite3 

conn = sqlite3.connect('pets_database.db')
pd.read_sql("SELECT * FROM cats;", conn)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>name</th>
      <th>age</th>
      <th>breed</th>
      <th>owner_id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>Maru</td>
      <td>3.0</td>
      <td>Scottish Fold</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>Hana</td>
      <td>1.0</td>
      <td>Tabby</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>Lil' Bub</td>
      <td>5.0</td>
      <td>American Shorthair</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>Moe</td>
      <td>10.0</td>
      <td>Tabby</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>Patches</td>
      <td>2.0</td>
      <td>Calico</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>5</th>
      <td>6</td>
      <td>None</td>
      <td>NaN</td>
      <td>Tabby</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>



## Filtering Using `WHERE`

We have already seen a simple example of a `WHERE` clause. For a similar example, below we select only records where the cat's name is `"Maru"`:


```python
pd.read_sql("SELECT * FROM cats WHERE name = 'Maru';", conn)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>name</th>
      <th>age</th>
      <th>breed</th>
      <th>owner_id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>Maru</td>
      <td>3</td>
      <td>Scottish Fold</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>



Specifically, this was an example of the `=` conditional operator. It is similar to the `==` operator in Python.

When you use this operator in a `WHERE` clause, SQL returns only the records that match that condition. It is essentially doing something like this:


```python
# Selecting all of the records in the database
result = pd.read_sql("SELECT * FROM cats;", conn)
# Create a list to store the records that match the query
cats_named_maru = []
# Loop over all of the cats
for _, data in result.iterrows():
    # Check if the name is "Maru"
    if data["name"] == "Maru":
        # Add to list
        cats_named_maru.append(data)

# Display the result list as a DataFrame
pd.DataFrame(cats_named_maru)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>name</th>
      <th>age</th>
      <th>breed</th>
      <th>owner_id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>Maru</td>
      <td>3.0</td>
      <td>Scottish Fold</td>
      <td>1.0</td>
    </tr>
  </tbody>
</table>
</div>



Clearly the SQL query is a lot shorter!

It also saves memory and improves performance to make sure that you only bring data into your Python code that you actually need, which a SQL `WHERE` clause can achieve.

### Conditional Operators in SQL

That query worked for matching a specific value. However, what if you wanted to select the cats who are at least 5 years old? Or all of the cats that don't currently belong to an owner? Or all of the cats with names that start with "M"?

We'll need some more advanced conditional operators for that.

Some important ones to know are:

* `!=` ("not equal to")
  * Similar to `not` combined with `==` in Python
* `>` ("greater than")
  * Similar to `>` in Python
* `>=` ("greater than or equal to")
  * Similar to `>=` in Python
* `<` ("less than")
  * Similar to `<` in Python
* `<=` ("less than or equal to")
  * Similar to `<=` in Python
* `AND`
  * Similar to `and` in Python
* `OR`
  * Similar to `or` in Python
* `BETWEEN`
  * Similar to placing a value between two values with `<=` and `and` in Python, e.g. `(2 <= x) and (x <= 5)`
* `IN`
  * Similar to `in` in Python
* `LIKE`
  * Uses wildcards to find similar strings. No direct equivalent in Python, but similar to some Bash terminal commands.

### `WHERE` Code-Along

In this exercise, you'll walk through executing a handful of common and handy SQL queries. We'll start by giving you an example of what this type of query looks like, then have you type a query specifically related to the `cats` table.

#### `WHERE` with `>=`

For the `=`, `!=`, `<`, `<=`, `>`, and `>=` operators, the query looks like:

```sql
SELECT column(s)
  FROM table_name
 WHERE column_name operator value;
```

> Note: The example above is not valid SQL, it is a template for how the queries are constructed

Type this SQL query between the quotes below to select all cats who are at least 5 years old:

```sql
SELECT *
  FROM cats
 WHERE age >= 5;
```


```python
pd.read_sql("""

""", conn)
```

This should return:

|id  |name     |age    |breed              |owner_id   |
|----|---------|-------|-------------------|-----------|
|3   |Lil' Bub |5.0    |American Shorthair |None       |
|4   |Moe      |10.0   |Tabby              |None       |

#### `WHERE` with `BETWEEN`

If you wanted to select all rows with values in a range, you _could_ do this by combining the `<=` and `AND` operators. However, since this is such a common task in SQL, there is a shorter and more efficient command specifically for this purpose, called `BETWEEN`.

A typical query with `BETWEEN` looks like:

```sql
SELECT column_name(s)
  FROM table_name
 WHERE column_name BETWEEN value1 AND value2;
```

> Note that `BETWEEN` is an **inclusive** range, so the returned values can match the boundary values (not like `range()` in Python)

Let's say you need to select the names of all of the cats whose age is between 1 and 3. Type this SQL query between the quotes below to select all cats who are in this age range:

```sql
SELECT *
  FROM cats
 WHERE age BETWEEN 1 AND 3;
```


```python
pd.read_sql("""

""", conn)
```

This should return:

|id  |name     |age    |breed              |owner_id   |
|----|---------|-------|-------------------|-----------|
|1   |Maru     |3.0    |Scottish Fold      |1.0        |
|2   |Hana     |1.0    |Tabby              |1.0        |
|5   |Patches  |2.0    |Calico             |NaN        |

#### `WHERE` Column Is Not `NULL`

`NULL` in SQL represents missing data. It is similar to `None` in Python or `NaN` in NumPy or pandas. However, we use the `IS` operator to check if something is `NULL`, not the `=` operator (or `IS NOT` instead of `!=`).

To check if a value is `NULL` (or not), the query looks like:

```sql
SELECT column(s)
  FROM table_name
 WHERE column_name IS (NOT) NULL;
```

> You might have noticed when we selected all rows of `cats`, some owner IDs were `NaN`, then in the above query they are `None` instead. This is a subtle difference where Python/pandas is converting SQL `NULL` values to `NaN` when there are numbers in other rows, and converting to `None` when all of the returned values are `NULL`. This is a subtle difference that you don't need to memorize; it is just highlighted to demonstrate that the operators we use in SQL are _similar_ to Python operators, but not quite the same.

If we want to select all cats that don't currently belong to an owner, we want to select all cats where the `owner_id` is `NULL`.

Type this SQL query between the quotes below to select all cats that don't currently belong to an owner:

```sql
SELECT *
  FROM cats
 WHERE owner_id IS NULL;
```


```python
pd.read_sql("""

""", conn)
```

This should return:

|id  |name     |age    |breed              |owner_id   |
|----|---------|-------|-------------------|-----------|
|3   |Lil' Bub |5.0    |American Shorthair |None       |
|4   |Moe      |10.0   |Tabby              |None       |
|5   |Patches  |2.0    |Calico             |None       |
|6   |None     |NaN    |Tabby              |None       |

#### `WHERE` with `LIKE`

The `LIKE` operator is very helpful for writing SQL queries with messy data. It uses _wildcards_ to specify which parts of the string query need to be an exact match and which parts can be variable.

When using `LIKE`, a query looks like:

```sql
SELECT column(s)
  FROM table_name
 WHERE column_name LIKE 'string_with_wildcards';
```

The most common wildcard you'll see is `%`. This is similar to the `*` wildcard in Bash or regex: it means zero or more characters with any value can be in that position.

So for example, if we want all cats with names that start with "M", we could use a query containing `M%`. This means that we're looking for matches that start with one character "M" (or "m", since this is a case-insensitive query in SQLite) and then zero or more characters that can have any value.

Type this SQL query between the quotes below to select all cats with names that start with "M" (or "m"):

```sql
SELECT *
  FROM cats
 WHERE name LIKE 'M%';
```


```python
pd.read_sql("""

""", conn)
```

This should return:

|id  |name     |age    |breed              |owner_id   |
|----|---------|-------|-------------------|-----------|
|1   |Maru     |3.0    |Scottish Fold      |1.0        |
|4   |Moe      |10.0   |Tabby              |NaN        |

The other wildcard used for comparing strings is `_`, which means exactly one character, with any value.

For example, if we wanted to select all cats with four-letter names where the second letter was "a", we could use `_a__`.

Type this SQL query between the quotes below to select all cats with names where the second letter is "a" and the name is four letters long:

```sql
SELECT *
  FROM cats
 WHERE name LIKE '_a__';
```


```python
pd.read_sql("""

""", conn)
```

This should return:

|id  |name     |age    |breed              |owner_id   |
|----|---------|-------|-------------------|-----------|
|1   |Maru     |3      |Scottish Fold      |1          |
|2   |Hana     |1      |Tabby              |1          |

These examples are a bit silly, but you can imagine how this technique would help to write queries between multiple datasets where the names don't quite match exactly! You can combine `%` and `_` in your string to narrow and expand your query results as needed.

#### `SELECT` with `COUNT`

Now, let's talk about the SQL aggregate function `COUNT`.

**SQL aggregate functions** are SQL statements that can get the average of a column's values, retrieve the minimum and maximum values from a column, sum values in a column, or count a number of records that meet certain conditions. You can learn more about these SQL aggregators [here](http://www.sqlclauses.com/sql+aggregate+functions) and [here](http://zetcode.com/db/sqlite/select/).

For now, we'll just focus on `COUNT`, which counts the number of records that meet a certain condition. Here's a standard SQL query using `COUNT`:

```sql
SELECT COUNT(column_name)
  FROM table_name
 WHERE conditional_statement;
```

Let's try it out and count the number of cats who have an `owner_id` of `1`. Type this SQL query between the quotes below:

```sql
SELECT COUNT(owner_id)
  FROM cats
 WHERE owner_id = 1;
```


```python
pd.read_sql("""

""", conn)
```

This should return:

|  |COUNT(owner_id) |
|--|----------------|
|0 |2               |

## Ordering and Limiting Results

Sometimes you aren't most interested in results that match an _absolute_ value like "greater than or equal to 5" or "matching 'Maru'", but rather a _relative_ value like "oldest", "youngest", "longest", "shortest", etc. To do this, you first need to use `ORDER BY` to sort the values, then `LIMIT` to select only the top values based on your sorting.

### `ORDER BY`

By default, SQL query results will be returned in the order that they are in the database. If you want to specify the sort order of the returned rows, you use the `ORDER BY` clause.

A standard query with `ORDER BY` looks like this:

```sql
SELECT column(s)
  FROM table_name
 ORDER BY column_name (sort_order);
```

Let's select all cats and order them by age. Type this SQL query between the quotes below:

```sql
SELECT *
  FROM cats
 ORDER BY age;
```


```python
pd.read_sql("""

""", conn)
```

This should return:

|id  |name     |age    |breed              |owner_id   |
|----|---------|-------|-------------------|-----------|
|6   |None     |NaN    |Tabby              |NaN        |
|2   |Hana     |1.0    |Tabby              |1.0        |
|5   |Patches  |2.0    |Calico             |NaN        |
|1   |Maru     |3.0    |Scottish Fold      |1.0        |
|3   |Lil' Bub |5.0    |American Shorthair |NaN        |
|4   |Moe      |10.0   |Tabby              |NaN        |

When using `ORDER BY`, the default is to order in ascending order. If you want to specify though, you can use `ASC` for "ascending" or `DESC` for "descending." Let's try to select all of our cats and sort them by age in descending order. Type this SQL query between the quotes below:

```sql
SELECT *
  FROM cats
 ORDER BY age DESC;
```


```python
pd.read_sql("""

""", conn)
```

This should return:

|id  |name     |age    |breed              |owner_id   |
|----|---------|-------|-------------------|-----------|
|4   |Moe      |10.0   |Tabby              |NaN        |
|3   |Lil' Bub |5.0    |American Shorthair |NaN        |
|1   |Maru     |3.0    |Scottish Fold      |1.0        |
|5   |Patches  |2.0    |Calico             |NaN        |
|2   |Hana     |1.0    |Tabby              |1.0        |
|6   |None     |NaN    |Tabby              |NaN        |

### `LIMIT`

`LIMIT` is used to determine the number of records you want to return from a dataset. 

A standard query with `LIMIT` would be:

```sql
SELECT column(s)
  FROM table_name
 LIMIT number;
```

Type this SQL query between the quotes below to select the first three rows of the `cats` table:

```sql
SELECT *
  FROM cats
 LIMIT 3;
```


```python
pd.read_sql("""

""", conn)
```

This should return:

|id  |name     |age    |breed              |owner_id   |
|----|---------|-------|-------------------|-----------|
|1   |Maru     |3.0    |Scottish Fold      |1.0        |
|2   |Hana     |1.0    |Tabby              |1.0        |
|3   |Lil' Bub |5.0    |American Shorthair |NaN        |

### `ORDER BY` with `LIMIT`

What if you want the oldest cat? The three oldest cats? If you want to select extremes from a database table -- for example, the employee with the highest paycheck or the patient with the most recent appointment -- we can use `ORDER BY` in conjunction with `LIMIT`.

Type this SQL query between the quotes below to select the oldest cat:

```sql
SELECT *
  FROM cats
 ORDER BY age DESC
 LIMIT 1;
```


```python
pd.read_sql("""

""", conn)
```

This should return:

|id  |name     |age    |breed              |owner_id   |
|----|---------|-------|-------------------|-----------|
|4   |Moe      |10.0   |Tabby              |NaN        |

This part of the statement: `SELECT * FROM cats ORDER BY age DESC` returns all of the cats in order from oldest to youngest. Setting a `LIMIT` of `1` returns just the first, i.e. oldest, cat on the list.

We could also get the two oldest cats by changing the limit to 2:


```python
pd.read_sql("""
SELECT *
  FROM cats
 ORDER BY age DESC
 LIMIT 2;
""", conn)
```

## Note on `SELECT`

We are now familiar with this syntax:

```sql
SELECT name
  FROM cats;
```

However, you may not know that this can be written like this as well:

```sql
SELECT cats.name
  FROM cats;
```

Both return the same data.

SQLite allows us to explicitly state the `tableName.columnName` you want to select. This is particularly useful when you want data from two different tables.

Imagine you have another table called `dogs` with a column containing all of the dog names:

```sql
CREATE TABLE dogs (
	id   INTEGER PRIMARY KEY,
	name TEXT
);
```

```sql
INSERT INTO dogs (name)
VALUES ("Clifford");
```


If you want to get the names of all the dogs and cats, you can no longer run a query with just the column name.
`SELECT name FROM cats,dogs;` will return `Error: ambiguous column name: name`.

Instead, you must explicitly follow the `tableName.columnName` syntax.
```sql
SELECT cats.name, dogs.name
  FROM cats, dogs;
```

You may see this in the future. Don't let it trip you up!


```python
conn.close()
```

## Summary

In this lesson, you expanded your SQL knowledge by learning how to limit the results of your query using `WHERE`, `ORDER BY`, and `LIMIT`. You also got a basic introduction to aggregate functions by seeing an example of `COUNT`, and dove deeper into some conditional operators including `BETWEEN` and `LIKE`.
