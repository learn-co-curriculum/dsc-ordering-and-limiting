# Ordering and Limiting Data with SQL

## Introduction

In this lesson, you'll learn how to sort your query results using an `ORDER BY` clause, and an additional technique for filtering the results of your SQL queries using the `ORDER BY` clause combined with the `LIMIT` clause.

## Objectives

You will be able to:
- Order the results of your queries by using `ORDER BY` (`ASC` & `DESC`)
- Limit the number of records returned by a query using `LIMIT`
- Write SQL queries to filter and order results

## The Data

Once again we'll be using the Northwind database, containing records related to employees, orders, products, etc.

Below we import the relevant packages and connect to the database:


```python
import pandas as pd
import sqlite3
conn = sqlite3.connect("data.sqlite")
```

In this lesson we'll focus on the `products` table:


```python
pd.read_sql("""
SELECT *
  FROM products;
""", conn)
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
      <th>productCode</th>
      <th>productName</th>
      <th>productLine</th>
      <th>productScale</th>
      <th>productVendor</th>
      <th>productDescription</th>
      <th>quantityInStock</th>
      <th>buyPrice</th>
      <th>MSRP</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>S10_1678</td>
      <td>1969 Harley Davidson Ultimate Chopper</td>
      <td>Motorcycles</td>
      <td>1:10</td>
      <td>Min Lin Diecast</td>
      <td>This replica features working kickstand, front...</td>
      <td>7933</td>
      <td>48.81</td>
      <td>95.70</td>
    </tr>
    <tr>
      <th>1</th>
      <td>S10_1949</td>
      <td>1952 Alpine Renault 1300</td>
      <td>Classic Cars</td>
      <td>1:10</td>
      <td>Classic Metal Creations</td>
      <td>Turnable front wheels; steering function; deta...</td>
      <td>7305</td>
      <td>98.58</td>
      <td>214.30</td>
    </tr>
    <tr>
      <th>2</th>
      <td>S10_2016</td>
      <td>1996 Moto Guzzi 1100i</td>
      <td>Motorcycles</td>
      <td>1:10</td>
      <td>Highway 66 Mini Classics</td>
      <td>Official Moto Guzzi logos and insignias, saddl...</td>
      <td>6625</td>
      <td>68.99</td>
      <td>118.94</td>
    </tr>
    <tr>
      <th>3</th>
      <td>S10_4698</td>
      <td>2003 Harley-Davidson Eagle Drag Bike</td>
      <td>Motorcycles</td>
      <td>1:10</td>
      <td>Red Start Diecast</td>
      <td>Model features, official Harley Davidson logos...</td>
      <td>5582</td>
      <td>91.02</td>
      <td>193.66</td>
    </tr>
    <tr>
      <th>4</th>
      <td>S10_4757</td>
      <td>1972 Alfa Romeo GTA</td>
      <td>Classic Cars</td>
      <td>1:10</td>
      <td>Motor City Art Classics</td>
      <td>Features include: Turnable front wheels; steer...</td>
      <td>3252</td>
      <td>85.68</td>
      <td>136.00</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>105</th>
      <td>S700_3505</td>
      <td>The Titanic</td>
      <td>Ships</td>
      <td>1:700</td>
      <td>Carousel DieCast Legends</td>
      <td>Completed model measures 19 1/2 inches long, 9...</td>
      <td>1956</td>
      <td>51.09</td>
      <td>100.17</td>
    </tr>
    <tr>
      <th>106</th>
      <td>S700_3962</td>
      <td>The Queen Mary</td>
      <td>Ships</td>
      <td>1:700</td>
      <td>Welly Diecast Productions</td>
      <td>Exact replica. Wood and Metal. Many extras inc...</td>
      <td>5088</td>
      <td>53.63</td>
      <td>99.31</td>
    </tr>
    <tr>
      <th>107</th>
      <td>S700_4002</td>
      <td>American Airlines: MD-11S</td>
      <td>Planes</td>
      <td>1:700</td>
      <td>Second Gear Diecast</td>
      <td>Polished finish. Exact replia with official lo...</td>
      <td>8820</td>
      <td>36.27</td>
      <td>74.03</td>
    </tr>
    <tr>
      <th>108</th>
      <td>S72_1253</td>
      <td>Boeing X-32A JSF</td>
      <td>Planes</td>
      <td>1:72</td>
      <td>Motor City Art Classics</td>
      <td>10" Wingspan with retractable landing gears.Co...</td>
      <td>4857</td>
      <td>32.77</td>
      <td>49.66</td>
    </tr>
    <tr>
      <th>109</th>
      <td>S72_3212</td>
      <td>Pont Yacht</td>
      <td>Ships</td>
      <td>1:72</td>
      <td>Unimax Art Galleries</td>
      <td>Measures 38 inches Long x 33 3/4 inches High. ...</td>
      <td>414</td>
      <td>33.30</td>
      <td>54.60</td>
    </tr>
  </tbody>
</table>
<p>110 rows × 9 columns</p>
</div>



## `ORDER BY`

By default, SQL query results will be returned in the order that they are in the database. This order may or may not have any relevance to your business case.

If, instead, you want to specify the sort order of the returned rows based on the values in the data, you use the `ORDER BY` clause.

A standard query with `ORDER BY` looks like this:

```sql
SELECT column(s)
  FROM table_name
 ORDER BY column_name (sort_order);
```

Let's say we wanted to order the `products` table by the product name:


```python
pd.read_sql("""
SELECT *
  FROM products
 ORDER BY productName;
""", conn)
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
      <th>productCode</th>
      <th>productName</th>
      <th>productLine</th>
      <th>productScale</th>
      <th>productVendor</th>
      <th>productDescription</th>
      <th>quantityInStock</th>
      <th>buyPrice</th>
      <th>MSRP</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>S18_3136</td>
      <td>18th Century Vintage Horse Carriage</td>
      <td>Vintage Cars</td>
      <td>1:18</td>
      <td>Red Start Diecast</td>
      <td>Hand crafted diecast-like metal horse carriage...</td>
      <td>5992</td>
      <td>60.74</td>
      <td>104.72</td>
    </tr>
    <tr>
      <th>1</th>
      <td>S24_2011</td>
      <td>18th century schooner</td>
      <td>Ships</td>
      <td>1:24</td>
      <td>Carousel DieCast Legends</td>
      <td>All wood with canvas sails. Many extras includ...</td>
      <td>1898</td>
      <td>82.34</td>
      <td>122.89</td>
    </tr>
    <tr>
      <th>2</th>
      <td>S24_2841</td>
      <td>1900s Vintage Bi-Plane</td>
      <td>Planes</td>
      <td>1:24</td>
      <td>Autoart Studio Design</td>
      <td>Hand crafted diecast-like metal bi-plane is re...</td>
      <td>5942</td>
      <td>34.25</td>
      <td>68.51</td>
    </tr>
    <tr>
      <th>3</th>
      <td>S24_4278</td>
      <td>1900s Vintage Tri-Plane</td>
      <td>Planes</td>
      <td>1:24</td>
      <td>Unimax Art Galleries</td>
      <td>Hand crafted diecast-like metal Triplane is Re...</td>
      <td>2756</td>
      <td>36.23</td>
      <td>72.45</td>
    </tr>
    <tr>
      <th>4</th>
      <td>S18_3140</td>
      <td>1903 Ford Model A</td>
      <td>Vintage Cars</td>
      <td>1:18</td>
      <td>Unimax Art Galleries</td>
      <td>Features opening trunk,  working steering system</td>
      <td>3913</td>
      <td>68.30</td>
      <td>136.59</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>105</th>
      <td>S700_1938</td>
      <td>The Mayflower</td>
      <td>Ships</td>
      <td>1:700</td>
      <td>Studio M Art Models</td>
      <td>Measures 31 1/2 inches Long x 25 1/2 inches Hi...</td>
      <td>737</td>
      <td>43.30</td>
      <td>86.61</td>
    </tr>
    <tr>
      <th>106</th>
      <td>S700_3962</td>
      <td>The Queen Mary</td>
      <td>Ships</td>
      <td>1:700</td>
      <td>Welly Diecast Productions</td>
      <td>Exact replica. Wood and Metal. Many extras inc...</td>
      <td>5088</td>
      <td>53.63</td>
      <td>99.31</td>
    </tr>
    <tr>
      <th>107</th>
      <td>S700_1138</td>
      <td>The Schooner Bluenose</td>
      <td>Ships</td>
      <td>1:700</td>
      <td>Autoart Studio Design</td>
      <td>All wood with canvas sails. Measures 31 1/2 in...</td>
      <td>1897</td>
      <td>34.00</td>
      <td>66.67</td>
    </tr>
    <tr>
      <th>108</th>
      <td>S700_3505</td>
      <td>The Titanic</td>
      <td>Ships</td>
      <td>1:700</td>
      <td>Carousel DieCast Legends</td>
      <td>Completed model measures 19 1/2 inches long, 9...</td>
      <td>1956</td>
      <td>51.09</td>
      <td>100.17</td>
    </tr>
    <tr>
      <th>109</th>
      <td>S700_2610</td>
      <td>The USS Constitution Ship</td>
      <td>Ships</td>
      <td>1:700</td>
      <td>Red Start Diecast</td>
      <td>All wood with canvas sails. Measures 31 1/2" L...</td>
      <td>7083</td>
      <td>33.97</td>
      <td>72.28</td>
    </tr>
  </tbody>
</table>
<p>110 rows × 9 columns</p>
</div>



### Ascending and Descending Sort Order

When using `ORDER BY`, the default is to order in *ascending* order. This aligns with "alphabetical order" for text data like `productName`.

You can specify `ASC` (ascending) at the end of the `ORDER BY` clause and retrieve the same result:


```python
pd.read_sql("""
SELECT *
  FROM products
 ORDER BY productName ASC;
""", conn)
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
      <th>productCode</th>
      <th>productName</th>
      <th>productLine</th>
      <th>productScale</th>
      <th>productVendor</th>
      <th>productDescription</th>
      <th>quantityInStock</th>
      <th>buyPrice</th>
      <th>MSRP</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>S18_3136</td>
      <td>18th Century Vintage Horse Carriage</td>
      <td>Vintage Cars</td>
      <td>1:18</td>
      <td>Red Start Diecast</td>
      <td>Hand crafted diecast-like metal horse carriage...</td>
      <td>5992</td>
      <td>60.74</td>
      <td>104.72</td>
    </tr>
    <tr>
      <th>1</th>
      <td>S24_2011</td>
      <td>18th century schooner</td>
      <td>Ships</td>
      <td>1:24</td>
      <td>Carousel DieCast Legends</td>
      <td>All wood with canvas sails. Many extras includ...</td>
      <td>1898</td>
      <td>82.34</td>
      <td>122.89</td>
    </tr>
    <tr>
      <th>2</th>
      <td>S24_2841</td>
      <td>1900s Vintage Bi-Plane</td>
      <td>Planes</td>
      <td>1:24</td>
      <td>Autoart Studio Design</td>
      <td>Hand crafted diecast-like metal bi-plane is re...</td>
      <td>5942</td>
      <td>34.25</td>
      <td>68.51</td>
    </tr>
    <tr>
      <th>3</th>
      <td>S24_4278</td>
      <td>1900s Vintage Tri-Plane</td>
      <td>Planes</td>
      <td>1:24</td>
      <td>Unimax Art Galleries</td>
      <td>Hand crafted diecast-like metal Triplane is Re...</td>
      <td>2756</td>
      <td>36.23</td>
      <td>72.45</td>
    </tr>
    <tr>
      <th>4</th>
      <td>S18_3140</td>
      <td>1903 Ford Model A</td>
      <td>Vintage Cars</td>
      <td>1:18</td>
      <td>Unimax Art Galleries</td>
      <td>Features opening trunk,  working steering system</td>
      <td>3913</td>
      <td>68.30</td>
      <td>136.59</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>105</th>
      <td>S700_1938</td>
      <td>The Mayflower</td>
      <td>Ships</td>
      <td>1:700</td>
      <td>Studio M Art Models</td>
      <td>Measures 31 1/2 inches Long x 25 1/2 inches Hi...</td>
      <td>737</td>
      <td>43.30</td>
      <td>86.61</td>
    </tr>
    <tr>
      <th>106</th>
      <td>S700_3962</td>
      <td>The Queen Mary</td>
      <td>Ships</td>
      <td>1:700</td>
      <td>Welly Diecast Productions</td>
      <td>Exact replica. Wood and Metal. Many extras inc...</td>
      <td>5088</td>
      <td>53.63</td>
      <td>99.31</td>
    </tr>
    <tr>
      <th>107</th>
      <td>S700_1138</td>
      <td>The Schooner Bluenose</td>
      <td>Ships</td>
      <td>1:700</td>
      <td>Autoart Studio Design</td>
      <td>All wood with canvas sails. Measures 31 1/2 in...</td>
      <td>1897</td>
      <td>34.00</td>
      <td>66.67</td>
    </tr>
    <tr>
      <th>108</th>
      <td>S700_3505</td>
      <td>The Titanic</td>
      <td>Ships</td>
      <td>1:700</td>
      <td>Carousel DieCast Legends</td>
      <td>Completed model measures 19 1/2 inches long, 9...</td>
      <td>1956</td>
      <td>51.09</td>
      <td>100.17</td>
    </tr>
    <tr>
      <th>109</th>
      <td>S700_2610</td>
      <td>The USS Constitution Ship</td>
      <td>Ships</td>
      <td>1:700</td>
      <td>Red Start Diecast</td>
      <td>All wood with canvas sails. Measures 31 1/2" L...</td>
      <td>7083</td>
      <td>33.97</td>
      <td>72.28</td>
    </tr>
  </tbody>
</table>
<p>110 rows × 9 columns</p>
</div>



Alternatively, if you wanted to use a *descending* order (opposite of alphabetical order for text data), you can use `DESC`:


```python
pd.read_sql("""
SELECT *
  FROM products
 ORDER BY productName DESC;
""", conn)
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
      <th>productCode</th>
      <th>productName</th>
      <th>productLine</th>
      <th>productScale</th>
      <th>productVendor</th>
      <th>productDescription</th>
      <th>quantityInStock</th>
      <th>buyPrice</th>
      <th>MSRP</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>S700_2610</td>
      <td>The USS Constitution Ship</td>
      <td>Ships</td>
      <td>1:700</td>
      <td>Red Start Diecast</td>
      <td>All wood with canvas sails. Measures 31 1/2" L...</td>
      <td>7083</td>
      <td>33.97</td>
      <td>72.28</td>
    </tr>
    <tr>
      <th>1</th>
      <td>S700_3505</td>
      <td>The Titanic</td>
      <td>Ships</td>
      <td>1:700</td>
      <td>Carousel DieCast Legends</td>
      <td>Completed model measures 19 1/2 inches long, 9...</td>
      <td>1956</td>
      <td>51.09</td>
      <td>100.17</td>
    </tr>
    <tr>
      <th>2</th>
      <td>S700_1138</td>
      <td>The Schooner Bluenose</td>
      <td>Ships</td>
      <td>1:700</td>
      <td>Autoart Studio Design</td>
      <td>All wood with canvas sails. Measures 31 1/2 in...</td>
      <td>1897</td>
      <td>34.00</td>
      <td>66.67</td>
    </tr>
    <tr>
      <th>3</th>
      <td>S700_3962</td>
      <td>The Queen Mary</td>
      <td>Ships</td>
      <td>1:700</td>
      <td>Welly Diecast Productions</td>
      <td>Exact replica. Wood and Metal. Many extras inc...</td>
      <td>5088</td>
      <td>53.63</td>
      <td>99.31</td>
    </tr>
    <tr>
      <th>4</th>
      <td>S700_1938</td>
      <td>The Mayflower</td>
      <td>Ships</td>
      <td>1:700</td>
      <td>Studio M Art Models</td>
      <td>Measures 31 1/2 inches Long x 25 1/2 inches Hi...</td>
      <td>737</td>
      <td>43.30</td>
      <td>86.61</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>105</th>
      <td>S18_3140</td>
      <td>1903 Ford Model A</td>
      <td>Vintage Cars</td>
      <td>1:18</td>
      <td>Unimax Art Galleries</td>
      <td>Features opening trunk,  working steering system</td>
      <td>3913</td>
      <td>68.30</td>
      <td>136.59</td>
    </tr>
    <tr>
      <th>106</th>
      <td>S24_4278</td>
      <td>1900s Vintage Tri-Plane</td>
      <td>Planes</td>
      <td>1:24</td>
      <td>Unimax Art Galleries</td>
      <td>Hand crafted diecast-like metal Triplane is Re...</td>
      <td>2756</td>
      <td>36.23</td>
      <td>72.45</td>
    </tr>
    <tr>
      <th>107</th>
      <td>S24_2841</td>
      <td>1900s Vintage Bi-Plane</td>
      <td>Planes</td>
      <td>1:24</td>
      <td>Autoart Studio Design</td>
      <td>Hand crafted diecast-like metal bi-plane is re...</td>
      <td>5942</td>
      <td>34.25</td>
      <td>68.51</td>
    </tr>
    <tr>
      <th>108</th>
      <td>S24_2011</td>
      <td>18th century schooner</td>
      <td>Ships</td>
      <td>1:24</td>
      <td>Carousel DieCast Legends</td>
      <td>All wood with canvas sails. Many extras includ...</td>
      <td>1898</td>
      <td>82.34</td>
      <td>122.89</td>
    </tr>
    <tr>
      <th>109</th>
      <td>S18_3136</td>
      <td>18th Century Vintage Horse Carriage</td>
      <td>Vintage Cars</td>
      <td>1:18</td>
      <td>Red Start Diecast</td>
      <td>Hand crafted diecast-like metal horse carriage...</td>
      <td>5992</td>
      <td>60.74</td>
      <td>104.72</td>
    </tr>
  </tbody>
</table>
<p>110 rows × 9 columns</p>
</div>



### Custom Sorting

You are not limited to sorting by alphabetical order. For example, if you wanted to sort based on the length of the `productDescription`, that would look like this:


```python
pd.read_sql("""
SELECT productName, length(productDescription) AS description_length
  FROM products
 ORDER BY description_length;
""", conn)
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
      <th>productName</th>
      <th>description_length</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1928 British Royal Navy Airplane</td>
      <td>28</td>
    </tr>
    <tr>
      <th>1</th>
      <td>P-51-D Mustang</td>
      <td>45</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1903 Ford Model A</td>
      <td>48</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1904 Buick Runabout</td>
      <td>48</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1930 Buick Marquette Phaeton</td>
      <td>48</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>105</th>
      <td>1936 Mercedes-Benz 500K Special Roadster</td>
      <td>361</td>
    </tr>
    <tr>
      <th>106</th>
      <td>2002 Suzuki XREO</td>
      <td>380</td>
    </tr>
    <tr>
      <th>107</th>
      <td>The Schooner Bluenose</td>
      <td>390</td>
    </tr>
    <tr>
      <th>108</th>
      <td>1996 Moto Guzzi 1100i</td>
      <td>391</td>
    </tr>
    <tr>
      <th>109</th>
      <td>2003 Harley-Davidson Eagle Drag Bike</td>
      <td>494</td>
    </tr>
  </tbody>
</table>
<p>110 rows × 2 columns</p>
</div>



You can also sort by something without selecting it. Here is a query that has the same order as the query above, but doesn't actually select the description length:


```python
pd.read_sql("""
SELECT productName
  FROM products
 ORDER BY length(productDescription);
""", conn)
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
      <th>productName</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1928 British Royal Navy Airplane</td>
    </tr>
    <tr>
      <th>1</th>
      <td>P-51-D Mustang</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1903 Ford Model A</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1904 Buick Runabout</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1930 Buick Marquette Phaeton</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
    </tr>
    <tr>
      <th>105</th>
      <td>1936 Mercedes-Benz 500K Special Roadster</td>
    </tr>
    <tr>
      <th>106</th>
      <td>2002 Suzuki XREO</td>
    </tr>
    <tr>
      <th>107</th>
      <td>The Schooner Bluenose</td>
    </tr>
    <tr>
      <th>108</th>
      <td>1996 Moto Guzzi 1100i</td>
    </tr>
    <tr>
      <th>109</th>
      <td>2003 Harley-Davidson Eagle Drag Bike</td>
    </tr>
  </tbody>
</table>
<p>110 rows × 1 columns</p>
</div>



### Sorting By Multiple Columns

You are also not limited to sorting by one column at a time. Typically this is most useful if some rows have repeated values in a given and you want a "tiebreaker" value from another column.

For example, this query sorts by `productVendor`, then `productName`:


```python
pd.read_sql("""
SELECT productVendor, productName, MSRP
  FROM products
 ORDER BY productVendor, productName;
""", conn)
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
      <th>productVendor</th>
      <th>productName</th>
      <th>MSRP</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Autoart Studio Design</td>
      <td>1900s Vintage Bi-Plane</td>
      <td>68.51</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Autoart Studio Design</td>
      <td>1932 Model A Ford J-Coupe</td>
      <td>127.13</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Autoart Studio Design</td>
      <td>1937 Horch 930V Limousine</td>
      <td>65.75</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Autoart Studio Design</td>
      <td>1962 Volkswagen Microbus</td>
      <td>127.79</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Autoart Studio Design</td>
      <td>1968 Ford Mustang</td>
      <td>194.57</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>105</th>
      <td>Welly Diecast Productions</td>
      <td>1968 Dodge Charger</td>
      <td>117.44</td>
    </tr>
    <tr>
      <th>106</th>
      <td>Welly Diecast Productions</td>
      <td>1969 Corvair Monza</td>
      <td>151.08</td>
    </tr>
    <tr>
      <th>107</th>
      <td>Welly Diecast Productions</td>
      <td>1969 Dodge Charger</td>
      <td>115.16</td>
    </tr>
    <tr>
      <th>108</th>
      <td>Welly Diecast Productions</td>
      <td>1971 Alpine Renault 1600s</td>
      <td>61.23</td>
    </tr>
    <tr>
      <th>109</th>
      <td>Welly Diecast Productions</td>
      <td>The Queen Mary</td>
      <td>99.31</td>
    </tr>
  </tbody>
</table>
<p>110 rows × 3 columns</p>
</div>



The ordering of the columns in the `ORDER BY` clause is important. The earlier ones should be the ones requiring a "tiebreaker", not the later ones. If we switch around the order of the previous query, for example, you'll notice that it doesn't really look sorted by `productVendor` at all:


```python
pd.read_sql("""
SELECT productVendor, productName, MSRP
  FROM products
 ORDER BY productName, productVendor;
""", conn)
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
      <th>productVendor</th>
      <th>productName</th>
      <th>MSRP</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Red Start Diecast</td>
      <td>18th Century Vintage Horse Carriage</td>
      <td>104.72</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Carousel DieCast Legends</td>
      <td>18th century schooner</td>
      <td>122.89</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Autoart Studio Design</td>
      <td>1900s Vintage Bi-Plane</td>
      <td>68.51</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Unimax Art Galleries</td>
      <td>1900s Vintage Tri-Plane</td>
      <td>72.45</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Unimax Art Galleries</td>
      <td>1903 Ford Model A</td>
      <td>136.59</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>105</th>
      <td>Studio M Art Models</td>
      <td>The Mayflower</td>
      <td>86.61</td>
    </tr>
    <tr>
      <th>106</th>
      <td>Welly Diecast Productions</td>
      <td>The Queen Mary</td>
      <td>99.31</td>
    </tr>
    <tr>
      <th>107</th>
      <td>Autoart Studio Design</td>
      <td>The Schooner Bluenose</td>
      <td>66.67</td>
    </tr>
    <tr>
      <th>108</th>
      <td>Carousel DieCast Legends</td>
      <td>The Titanic</td>
      <td>100.17</td>
    </tr>
    <tr>
      <th>109</th>
      <td>Red Start Diecast</td>
      <td>The USS Constitution Ship</td>
      <td>72.28</td>
    </tr>
  </tbody>
</table>
<p>110 rows × 3 columns</p>
</div>



Another way of thinking about which column should come first after `ORDER BY`, is that the fewer unique values the column has, the earlier it should appear.

To find out how many unique values there are in a column, we can use the `DISTINCT` keyword in addition to `COUNT`. Below are some examples:


```python
pd.read_sql("""
SELECT COUNT(DISTINCT productVendor) AS num_product_vendors,
       COUNT(DISTINCT productName) AS num_product_names
  FROM products;
""", conn)
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
      <th>num_product_vendors</th>
      <th>num_product_names</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>13</td>
      <td>110</td>
    </tr>
  </tbody>
</table>
</div>



This result aligns with what we observed earlier. There are many more unique product names than product vendors, so it makes sense to sort by vendors first, then names.

### Type Casting

Sometimes when you use `ORDER BY`, you'll get an odd result like this. We are trying to sort by `quantityInStock` to get a list of products from the least to most number in stock right now:


```python
pd.read_sql("""
SELECT productName, quantityInStock
  FROM products
 ORDER BY quantityInStock;
""", conn)
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
      <th>productName</th>
      <th>quantityInStock</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1970 Chevy Chevelle SS 454</td>
      <td>1005</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Diamond T620 Semi-Skirted Tanker</td>
      <td>1016</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1969 Ford Falcon</td>
      <td>1049</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1957 Corvette Convertible</td>
      <td>1249</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1928 Ford Phaeton Deluxe</td>
      <td>136</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>105</th>
      <td>2002 Chevy Corvette</td>
      <td>9446</td>
    </tr>
    <tr>
      <th>106</th>
      <td>America West Airlines B757-200</td>
      <td>9653</td>
    </tr>
    <tr>
      <th>107</th>
      <td>1995 Honda Civic</td>
      <td>9772</td>
    </tr>
    <tr>
      <th>108</th>
      <td>P-51-D Mustang</td>
      <td>992</td>
    </tr>
    <tr>
      <th>109</th>
      <td>2002 Suzuki XREO</td>
      <td>9997</td>
    </tr>
  </tbody>
</table>
<p>110 rows × 2 columns</p>
</div>



What is 136 doing there? What happened?

Let's take a closer look at the first 10 results (using pandas):


```python
pd.read_sql("""
SELECT productName, quantityInStock
  FROM products
 ORDER BY quantityInStock;
""", conn).head(10)
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
      <th>productName</th>
      <th>quantityInStock</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1970 Chevy Chevelle SS 454</td>
      <td>1005</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Diamond T620 Semi-Skirted Tanker</td>
      <td>1016</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1969 Ford Falcon</td>
      <td>1049</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1957 Corvette Convertible</td>
      <td>1249</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1928 Ford Phaeton Deluxe</td>
      <td>136</td>
    </tr>
    <tr>
      <th>5</th>
      <td>1952 Citroen-15CV</td>
      <td>1452</td>
    </tr>
    <tr>
      <th>6</th>
      <td>1960 BSA Gold Star DBD34</td>
      <td>15</td>
    </tr>
    <tr>
      <th>7</th>
      <td>1958 Setra Bus</td>
      <td>1579</td>
    </tr>
    <tr>
      <th>8</th>
      <td>1962 City of Detroit Streetcar</td>
      <td>1645</td>
    </tr>
    <tr>
      <th>9</th>
      <td>1997 BMW F650 ST</td>
      <td>178</td>
    </tr>
  </tbody>
</table>
</div>



The problem is that **we assumed that these values were stored as numbers, but apparently they are actually strings**. So they are being sorted in alphabetical order, so "1249", "136", and "1452" are correctly ordered based on comparing the first two characters ("12" comes before "13", which comes before "14").

We want to treat these as numbers, not strings, so we can use `CAST` as part of the `ORDER BY` clause.

Specifically since these are whole numbers, we'll cast them as the `INTEGER` data type in SQLite:


```python
pd.read_sql("""
SELECT productName, quantityInStock
  FROM products
 ORDER BY CAST(quantityInStock AS INTEGER);
""", conn).head(10)
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
      <th>productName</th>
      <th>quantityInStock</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1960 BSA Gold Star DBD34</td>
      <td>15</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1968 Ford Mustang</td>
      <td>68</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1928 Ford Phaeton Deluxe</td>
      <td>136</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1997 BMW F650 ST</td>
      <td>178</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Pont Yacht</td>
      <td>414</td>
    </tr>
    <tr>
      <th>5</th>
      <td>1911 Ford Town Car</td>
      <td>540</td>
    </tr>
    <tr>
      <th>6</th>
      <td>1928 Mercedes-Benz SSK</td>
      <td>548</td>
    </tr>
    <tr>
      <th>7</th>
      <td>F/A 18 Hornet 1/72</td>
      <td>551</td>
    </tr>
    <tr>
      <th>8</th>
      <td>2002 Yamaha YZR M1</td>
      <td>600</td>
    </tr>
    <tr>
      <th>9</th>
      <td>The Mayflower</td>
      <td>737</td>
    </tr>
  </tbody>
</table>
</div>



That looks right for the first 10 rows! Let's check the full dataset:


```python
pd.read_sql("""
SELECT productName, quantityInStock
  FROM products
 ORDER BY CAST(quantityInStock AS INTEGER);
""", conn)
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
      <th>productName</th>
      <th>quantityInStock</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1960 BSA Gold Star DBD34</td>
      <td>15</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1968 Ford Mustang</td>
      <td>68</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1928 Ford Phaeton Deluxe</td>
      <td>136</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1997 BMW F650 ST</td>
      <td>178</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Pont Yacht</td>
      <td>414</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>105</th>
      <td>1932 Model A Ford J-Coupe</td>
      <td>9354</td>
    </tr>
    <tr>
      <th>106</th>
      <td>2002 Chevy Corvette</td>
      <td>9446</td>
    </tr>
    <tr>
      <th>107</th>
      <td>America West Airlines B757-200</td>
      <td>9653</td>
    </tr>
    <tr>
      <th>108</th>
      <td>1995 Honda Civic</td>
      <td>9772</td>
    </tr>
    <tr>
      <th>109</th>
      <td>2002 Suzuki XREO</td>
      <td>9997</td>
    </tr>
  </tbody>
</table>
<p>110 rows × 2 columns</p>
</div>



Looks good!

## `LIMIT`

`LIMIT` is used to determine the number of records you want to return from a dataset. 

A standard query with `LIMIT` would be:

```sql
SELECT column(s)
  FROM table_name
 LIMIT number;
```

You can use `LIMIT` to specify that you only want a certain number of results, in whatever order they appear in the database. This is kind of like using `.head()` to look at the first part of a dataframe:


```python
pd.read_sql("""
SELECT *
  FROM orders
 LIMIT 5;
""", conn)
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
      <th>orderNumber</th>
      <th>orderDate</th>
      <th>requiredDate</th>
      <th>shippedDate</th>
      <th>status</th>
      <th>comments</th>
      <th>customerNumber</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>10100</td>
      <td>2003-01-06</td>
      <td>2003-01-13</td>
      <td>2003-01-10</td>
      <td>Shipped</td>
      <td></td>
      <td>363</td>
    </tr>
    <tr>
      <th>1</th>
      <td>10101</td>
      <td>2003-01-09</td>
      <td>2003-01-18</td>
      <td>2003-01-11</td>
      <td>Shipped</td>
      <td>Check on availability.</td>
      <td>128</td>
    </tr>
    <tr>
      <th>2</th>
      <td>10102</td>
      <td>2003-01-10</td>
      <td>2003-01-18</td>
      <td>2003-01-14</td>
      <td>Shipped</td>
      <td></td>
      <td>181</td>
    </tr>
    <tr>
      <th>3</th>
      <td>10103</td>
      <td>2003-01-29</td>
      <td>2003-02-07</td>
      <td>2003-02-02</td>
      <td>Shipped</td>
      <td></td>
      <td>121</td>
    </tr>
    <tr>
      <th>4</th>
      <td>10104</td>
      <td>2003-01-31</td>
      <td>2003-02-09</td>
      <td>2003-02-01</td>
      <td>Shipped</td>
      <td></td>
      <td>141</td>
    </tr>
  </tbody>
</table>
</div>



This approach would also be relevant if you find yourself in a situation where the database is too large, so you can only select a certain number of rows at a time.

### `LIMIT` with `ORDER BY`

`LIMIT` becomes much more powerful and useful when combined with `ORDER BY`.

Sometimes you are less interested in results that are filtered based on an _absolute_ value like "greater than or equal to 5" or "starting with 'M'", and more interested in results that are filtered based on a _relative_ value like "oldest", "youngest", "longest", "shortest", etc.

To do this kind of filtering, you first need to use `ORDER BY` to sort the values, then `LIMIT` to select only the top value(s) based on your sorting.

For example, if we wanted to select the 10 orders with the longest comments to start a customer service audit:


```python
pd.read_sql("""
SELECT *
  FROM orders
 ORDER BY length(comments) DESC
 LIMIT 10;
""", conn)
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
      <th>orderNumber</th>
      <th>orderDate</th>
      <th>requiredDate</th>
      <th>shippedDate</th>
      <th>status</th>
      <th>comments</th>
      <th>customerNumber</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>10167</td>
      <td>2003-10-23</td>
      <td>2003-10-30</td>
      <td></td>
      <td>Cancelled</td>
      <td>Customer called to cancel. The warehouse was n...</td>
      <td>448</td>
    </tr>
    <tr>
      <th>1</th>
      <td>10179</td>
      <td>2003-11-11</td>
      <td>2003-11-17</td>
      <td>2003-11-13</td>
      <td>Cancelled</td>
      <td>Customer cancelled due to urgent budgeting iss...</td>
      <td>496</td>
    </tr>
    <tr>
      <th>2</th>
      <td>10253</td>
      <td>2004-06-01</td>
      <td>2004-06-09</td>
      <td>2004-06-02</td>
      <td>Cancelled</td>
      <td>Customer disputed the order and we agreed to c...</td>
      <td>201</td>
    </tr>
    <tr>
      <th>3</th>
      <td>10173</td>
      <td>2003-11-05</td>
      <td>2003-11-15</td>
      <td>2003-11-09</td>
      <td>Shipped</td>
      <td>Cautious optimism. We have happy customers her...</td>
      <td>278</td>
    </tr>
    <tr>
      <th>4</th>
      <td>10279</td>
      <td>2004-08-09</td>
      <td>2004-08-19</td>
      <td>2004-08-15</td>
      <td>Shipped</td>
      <td>Cautious optimism. We have happy customers her...</td>
      <td>141</td>
    </tr>
    <tr>
      <th>5</th>
      <td>10377</td>
      <td>2005-02-09</td>
      <td>2005-02-21</td>
      <td>2005-02-12</td>
      <td>Shipped</td>
      <td>Cautious optimism. We have happy customers her...</td>
      <td>186</td>
    </tr>
    <tr>
      <th>6</th>
      <td>10124</td>
      <td>2003-05-21</td>
      <td>2003-05-29</td>
      <td>2003-05-25</td>
      <td>Shipped</td>
      <td>Customer very concerned about the exact color ...</td>
      <td>112</td>
    </tr>
    <tr>
      <th>7</th>
      <td>10230</td>
      <td>2004-03-15</td>
      <td>2004-03-24</td>
      <td>2004-03-20</td>
      <td>Shipped</td>
      <td>Customer very concerned about the exact color ...</td>
      <td>128</td>
    </tr>
    <tr>
      <th>8</th>
      <td>10328</td>
      <td>2004-11-12</td>
      <td>2004-11-21</td>
      <td>2004-11-18</td>
      <td>Shipped</td>
      <td>Customer very concerned about the exact color ...</td>
      <td>278</td>
    </tr>
    <tr>
      <th>9</th>
      <td>10367</td>
      <td>2005-01-12</td>
      <td>2005-01-21</td>
      <td>2005-01-16</td>
      <td>Resolved</td>
      <td>This order was disputed and resolved on 2/1/20...</td>
      <td>205</td>
    </tr>
  </tbody>
</table>
</div>



You can also combine `LIMIT` with `WHERE` if you want to apply two different kinds of filters (absolute and relative) at the same time:


```python
pd.read_sql("""
SELECT *
  FROM orders
 WHERE status = "Cancelled"
 ORDER BY length(comments) DESC
 LIMIT 10;
""", conn)
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
      <th>orderNumber</th>
      <th>orderDate</th>
      <th>requiredDate</th>
      <th>shippedDate</th>
      <th>status</th>
      <th>comments</th>
      <th>customerNumber</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>10167</td>
      <td>2003-10-23</td>
      <td>2003-10-30</td>
      <td></td>
      <td>Cancelled</td>
      <td>Customer called to cancel. The warehouse was n...</td>
      <td>448</td>
    </tr>
    <tr>
      <th>1</th>
      <td>10179</td>
      <td>2003-11-11</td>
      <td>2003-11-17</td>
      <td>2003-11-13</td>
      <td>Cancelled</td>
      <td>Customer cancelled due to urgent budgeting iss...</td>
      <td>496</td>
    </tr>
    <tr>
      <th>2</th>
      <td>10253</td>
      <td>2004-06-01</td>
      <td>2004-06-09</td>
      <td>2004-06-02</td>
      <td>Cancelled</td>
      <td>Customer disputed the order and we agreed to c...</td>
      <td>201</td>
    </tr>
    <tr>
      <th>3</th>
      <td>10260</td>
      <td>2004-06-16</td>
      <td>2004-06-22</td>
      <td></td>
      <td>Cancelled</td>
      <td>Customer heard complaints from their customers...</td>
      <td>357</td>
    </tr>
    <tr>
      <th>4</th>
      <td>10262</td>
      <td>2004-06-24</td>
      <td>2004-07-01</td>
      <td></td>
      <td>Cancelled</td>
      <td>This customer found a better offer from one of...</td>
      <td>141</td>
    </tr>
    <tr>
      <th>5</th>
      <td>10248</td>
      <td>2004-05-07</td>
      <td>2004-05-14</td>
      <td></td>
      <td>Cancelled</td>
      <td>Order was mistakenly placed. The warehouse not...</td>
      <td>131</td>
    </tr>
  </tbody>
</table>
</div>



As you can see in the above query result, note that **the number after `LIMIT` does not guarantee that you will get that number of results**. That is only the upper limit of the results you might get. It appears that there are only 6 canceled orders, so even though we limited the results to 10, we only got 6.

If there were 0 canceled orders, we would get 0 results, regardless of what `LIMIT` says.

If we loosen the query restriction on the `status` column (producing more results), we can see the `LIMIT` take effect again:


```python
pd.read_sql("""
SELECT *
  FROM orders
 WHERE status IN ("Cancelled", "Resolved")
 ORDER BY length(comments) DESC
 LIMIT 10;
""", conn)
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
      <th>orderNumber</th>
      <th>orderDate</th>
      <th>requiredDate</th>
      <th>shippedDate</th>
      <th>status</th>
      <th>comments</th>
      <th>customerNumber</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>10167</td>
      <td>2003-10-23</td>
      <td>2003-10-30</td>
      <td></td>
      <td>Cancelled</td>
      <td>Customer called to cancel. The warehouse was n...</td>
      <td>448</td>
    </tr>
    <tr>
      <th>1</th>
      <td>10179</td>
      <td>2003-11-11</td>
      <td>2003-11-17</td>
      <td>2003-11-13</td>
      <td>Cancelled</td>
      <td>Customer cancelled due to urgent budgeting iss...</td>
      <td>496</td>
    </tr>
    <tr>
      <th>2</th>
      <td>10253</td>
      <td>2004-06-01</td>
      <td>2004-06-09</td>
      <td>2004-06-02</td>
      <td>Cancelled</td>
      <td>Customer disputed the order and we agreed to c...</td>
      <td>201</td>
    </tr>
    <tr>
      <th>3</th>
      <td>10367</td>
      <td>2005-01-12</td>
      <td>2005-01-21</td>
      <td>2005-01-16</td>
      <td>Resolved</td>
      <td>This order was disputed and resolved on 2/1/20...</td>
      <td>205</td>
    </tr>
    <tr>
      <th>4</th>
      <td>10327</td>
      <td>2004-11-10</td>
      <td>2004-11-19</td>
      <td>2004-11-13</td>
      <td>Resolved</td>
      <td>Order was disputed and resolved on 12/1/04. Th...</td>
      <td>145</td>
    </tr>
    <tr>
      <th>5</th>
      <td>10164</td>
      <td>2003-10-21</td>
      <td>2003-10-30</td>
      <td>2003-10-23</td>
      <td>Resolved</td>
      <td>This order was disputed, but resolved on 11/1/...</td>
      <td>452</td>
    </tr>
    <tr>
      <th>6</th>
      <td>10260</td>
      <td>2004-06-16</td>
      <td>2004-06-22</td>
      <td></td>
      <td>Cancelled</td>
      <td>Customer heard complaints from their customers...</td>
      <td>357</td>
    </tr>
    <tr>
      <th>7</th>
      <td>10262</td>
      <td>2004-06-24</td>
      <td>2004-07-01</td>
      <td></td>
      <td>Cancelled</td>
      <td>This customer found a better offer from one of...</td>
      <td>141</td>
    </tr>
    <tr>
      <th>8</th>
      <td>10386</td>
      <td>2005-03-01</td>
      <td>2005-03-09</td>
      <td>2005-03-06</td>
      <td>Resolved</td>
      <td>Disputed then Resolved on 3/15/2005. Customer ...</td>
      <td>141</td>
    </tr>
    <tr>
      <th>9</th>
      <td>10248</td>
      <td>2004-05-07</td>
      <td>2004-05-14</td>
      <td></td>
      <td>Cancelled</td>
      <td>Order was mistakenly placed. The warehouse not...</td>
      <td>131</td>
    </tr>
  </tbody>
</table>
</div>



### `LIMIT` with Date/Time Data

`LIMIT` can be especially useful for finding the oldest and newest records in a dataset with date/time data.

For example, who were the first five unique customers to place an order using this system?


```python
pd.read_sql("""
SELECT DISTINCT customerNumber, orderDate
  FROM orders
 ORDER BY orderDate
 LIMIT 5;
""", conn)
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
      <th>customerNumber</th>
      <th>orderDate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>363</td>
      <td>2003-01-06</td>
    </tr>
    <tr>
      <th>1</th>
      <td>128</td>
      <td>2003-01-09</td>
    </tr>
    <tr>
      <th>2</th>
      <td>181</td>
      <td>2003-01-10</td>
    </tr>
    <tr>
      <th>3</th>
      <td>121</td>
      <td>2003-01-29</td>
    </tr>
    <tr>
      <th>4</th>
      <td>141</td>
      <td>2003-01-31</td>
    </tr>
  </tbody>
</table>
</div>



Of the orders that have not been shipped and have not been canceled, what are the 10 newest based on order date?


```python
pd.read_sql("""
SELECT *
  FROM orders
 WHERE shippedDate = ""
   AND status != "Cancelled"
 ORDER BY orderDate DESC
 LIMIT 10;
""", conn)
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
      <th>orderNumber</th>
      <th>orderDate</th>
      <th>requiredDate</th>
      <th>shippedDate</th>
      <th>status</th>
      <th>comments</th>
      <th>customerNumber</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>10424</td>
      <td>2005-05-31</td>
      <td>2005-06-08</td>
      <td></td>
      <td>In Process</td>
      <td></td>
      <td>141</td>
    </tr>
    <tr>
      <th>1</th>
      <td>10425</td>
      <td>2005-05-31</td>
      <td>2005-06-07</td>
      <td></td>
      <td>In Process</td>
      <td></td>
      <td>119</td>
    </tr>
    <tr>
      <th>2</th>
      <td>10422</td>
      <td>2005-05-30</td>
      <td>2005-06-11</td>
      <td></td>
      <td>In Process</td>
      <td></td>
      <td>157</td>
    </tr>
    <tr>
      <th>3</th>
      <td>10423</td>
      <td>2005-05-30</td>
      <td>2005-06-05</td>
      <td></td>
      <td>In Process</td>
      <td></td>
      <td>314</td>
    </tr>
    <tr>
      <th>4</th>
      <td>10420</td>
      <td>2005-05-29</td>
      <td>2005-06-07</td>
      <td></td>
      <td>In Process</td>
      <td></td>
      <td>282</td>
    </tr>
    <tr>
      <th>5</th>
      <td>10421</td>
      <td>2005-05-29</td>
      <td>2005-06-06</td>
      <td></td>
      <td>In Process</td>
      <td>Custom shipping instructions were sent to ware...</td>
      <td>124</td>
    </tr>
    <tr>
      <th>6</th>
      <td>10414</td>
      <td>2005-05-06</td>
      <td>2005-05-13</td>
      <td></td>
      <td>On Hold</td>
      <td>Customer credit limit exceeded. Will ship when...</td>
      <td>362</td>
    </tr>
    <tr>
      <th>7</th>
      <td>10407</td>
      <td>2005-04-22</td>
      <td>2005-05-04</td>
      <td></td>
      <td>On Hold</td>
      <td>Customer credit limit exceeded. Will ship when...</td>
      <td>450</td>
    </tr>
    <tr>
      <th>8</th>
      <td>10401</td>
      <td>2005-04-03</td>
      <td>2005-04-14</td>
      <td></td>
      <td>On Hold</td>
      <td>Customer credit limit exceeded. Will ship when...</td>
      <td>328</td>
    </tr>
    <tr>
      <th>9</th>
      <td>10334</td>
      <td>2004-11-19</td>
      <td>2004-11-28</td>
      <td></td>
      <td>On Hold</td>
      <td>The outstaniding balance for this customer exc...</td>
      <td>144</td>
    </tr>
  </tbody>
</table>
</div>



What is the order that took the longest to fulfill, and how long did it take?


```python
pd.read_sql("""
SELECT *,
       julianday(shippedDate) - julianday(orderDate) AS days_to_fulfill
  FROM orders
 WHERE shippedDate != ""
 ORDER BY days_to_fulfill DESC
 LIMIT 1;
""", conn)
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
      <th>orderNumber</th>
      <th>orderDate</th>
      <th>requiredDate</th>
      <th>shippedDate</th>
      <th>status</th>
      <th>comments</th>
      <th>customerNumber</th>
      <th>days_to_fulfill</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>10165</td>
      <td>2003-10-22</td>
      <td>2003-10-31</td>
      <td>2003-12-26</td>
      <td>Shipped</td>
      <td>This order was on hold because customers's cre...</td>
      <td>148</td>
      <td>65.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
conn.close()
```

## Summary

In this lesson, you expanded your SQL knowledge by learning how to sort and limit the results of your query using `ORDER BY` and `LIMIT`. You also saw how to customize the sorting behavior as well as incorporate `DISTINCT` and `WHERE` keywords to create more-sophisticated queries.
