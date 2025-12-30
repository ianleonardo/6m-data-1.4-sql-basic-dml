## **Section 1: The Art of Asking (Selection, Filtering & Sorting)**

**Time:** 60 Minutes

### **Learning Objectives**

Learners will be able to write queries to retrieve specific columns, apply mathematical operators, and filter datasets based on multiple text and numeric conditions8.

### **Theory Recap (10 min) – *The "Conversation" with Data***

**Instructor Script:** "Think of a database not as a scary technical box, but as a very organized librarian. To get what you want, you just need to be specific. SELECT is you pointing at what you want to see; FROM is you telling the librarian which shelf to look at; and WHERE is your set of rules—like 'only show me books published after 2010.' It's a conversation, not just code."

### **Workshop (40 min)**

**Task 1: Basic Retrieval**

SQL

\-- Step 1: See everything  
SELECT \* FROM resale\_flat\_prices\_2017;

\-- Step 2: Specific interest (Socratic: Why not select \* every time?)  
SELECT street\_name, block, resale\_price FROM resale\_flat\_prices\_2017;

* **Socratic Prompt:** "If our table had 100 columns and a million rows, what would happen to our computer's memory if we always used SELECT \*?"

**Task 2: Transformations & Filtering**

SQL

\-- Calculate price in thousands and rename for clarity  
SELECT street\_name, resale\_price / 1000 AS price\_k   
FROM resale\_flat\_prices\_2017  
WHERE town \= 'PUNGGOL'   
  AND resale\_price \> 500000  
ORDER BY resale\_price DESC;

* **Instructor Prompt:** "I want to find a home for my parents. They need something larger than 100sqm, but my budget is strictly under $600,000. How would we write that rule?"

### **Q\&A (5 min)**

* **Common Hurdle:** "Do I need to capitalize SELECT?" (Answer: SQL is case-insensitive for keywords, but upper-case is industry standard for readability).

### **Reflection (5 min)**

* **Business Use Case:** How would a real estate app like PropertyGuru use these filters when a user moves a slider on their screen?

## ---

**Section 2: Finding the Big Picture (Aggregates & Grouping)**

**Time:** 60 Minutes

### **Learning Objectives**

Learners will be able to summarize thousands of rows into meaningful insights (averages, counts) and segment those insights by categories9.

### **Theory Recap (10 min) – *The "Bucket" Analogy***

**Instructor Script:** "If I give you 10,000 receipts and ask for the average spent, you don't look at them one by one. You sum them and divide. That's AVG(). If I ask for the average spent *per store*, you first put the receipts into 'store buckets.' That is GROUP BY. GROUP BY is simply the act of sorting into buckets before doing the math."

### **Workshop (40 min)**

**Task 3: Basic Aggregates**

SQL

\-- How many transactions happened?  
SELECT COUNT(\*) FROM resale\_flat\_prices\_2017;

\-- What is the most expensive flat ever sold in this dataset?  
SELECT MAX(resale\_price) FROM resale\_flat\_prices\_2017;

**Task 4: Grouping & Having**

SQL

\-- Average price per town  
SELECT town, AVG(resale\_price) AS avg\_price  
FROM resale\_flat\_prices\_2017  
GROUP BY town  
HAVING avg\_price \> 600000 \-- Only show expensive towns  
ORDER BY avg\_price DESC;

* **Socratic Prompt:** "Wait, why can't I use WHERE avg\_price \> 600000? What's the difference between filtering a row and filtering a group?"10.

### **Q\&A (5 min)**

* **Common Hurdle:** "What happens if I forget the GROUP BY but use an AVG()?" (Answer: Most SQL engines will throw an error because they don't know how to display individual towns next to a single average).

### **Reflection (5 min)**

* **Business Use Case:** If you are a government planner, how does GROUP BY town help you decide where to build the next MRT station or school?

## ---

**Section 3: Advanced Logic & Data Cleaning**

**Time:** 60 Minutes

### **Learning Objectives**

Learners will be able to categorize data using CASE, convert data types using CAST, and manipulate date strings11111111.

### **Theory Recap (10 min) – *The "Translator"***

**Instructor Script:** "Sometimes data isn't in the format we want. A date might be stored as text, or we might want to label prices as 'Cheap' or 'Expensive' instead of just numbers. CAST is our translator, and CASE is our logic builder—it’s like an 'If-Then' statement for your data."

### **Workshop (40 min)**

**Task 5: Categorizing with CASE**

SQL

SELECT town, resale\_price,  
CASE   
    WHEN resale\_price \> 1000000 THEN 'Million Dollar Club'  
    WHEN resale\_price \> 500000 THEN 'Mid-Range'  
    ELSE 'Entry-Level'   
END AS price\_category  
FROM resale\_flat\_prices\_2017;

**Task 6: Dates and Casting**

SQL

\-- Convert text to a real date and extract the year  
SELECT   
    month,   
    CONCAT(month, '-01')::DATE AS transaction\_date,  
    date\_part('year', (month || '-01')::DATE) AS sale\_year  
FROM resale\_flat\_prices\_2017;

* **Socratic Prompt:** "If the 'month' column is text '2017-01', can we add 1 month to it directly? Why do we need to CAST it to a DATE type first?"

### **Q\&A (5 min)**

* **Common Hurdle:** "What does the :: mean?" (Answer: It’s a shorthand for the CAST function in DuckDB and Postgres).

### **Reflection (5 min)**

* **Business Use Case:** Why is it important to standardize date formats when merging data from two different countries?


Below is the previous version
---

# Lesson

## Brief

### Lesson Overview

This lesson introduces the SQL Data Manipulation Language (DML) statements. Learners will be able to query and manipulate data in a database
using operators, functions, filters, sorting, aggregate functions, group by and advanced operators/functions.

---

## Part 1 - Connecting to database

Open DBeaver and create a new connection to the DuckDB database file `db/unit-1-4.db`.

The table we will be using is `main.resale_flat_prices_2017`. It contains HDB's resale flat prices based on registration date from Jan-2017 onwards.

The description for each column (a.k.a data dictionary) is as follows:

| Title               | Column Name         | Data Type                  | Unit of Measure | Description |
| ------------------- | ------------------- | -------------------------- | --------------- | ----------- |
| Month               | month               | Datetime (Month) "YYYY-MM" | -               | -           |
| Town                | town                | Text (General)             | -               | -           |
| Flat type           | flat_type           | Text (General)             | -               | -           |
| Block               | block               | Text (General)             | -               | -           |
| Street name         | street_name         | Text (General)             | -               | -           |
| Storey range        | storey_range        | Text (General)             | -               | -           |
| Floor area sqm      | floor_area_sqm      | Numeric (General)          | sqm             | -           |
| Flat model          | flat_model          | Text (General)             | -               | -           |
| Lease commence date | lease_commence_date | Datetime (Year) "YYYY"     | -               | -           |
| Remaining lease     | remaining_lease     | Text (General)             | -               | -           |
| Resale price        | resale_price        | Numeric (General)          | $               | -           |

> Compare them with the data types in DuckDB's table.

## Part 2 - Querying data

### Basic syntax

`SELECT` is the most commonly used DML statement. It is used to query data from a database.

Select a column from a table:

```sql
SELECT <column_name> FROM <table_name>;
```

Select multiple columns from a table:

```sql
SELECT <column_name_1>, <column_name_2> FROM <table_name>;
```

Example:

```sql
SELECT street_name FROM resale_flat_prices_2017;
```

_No need to specify the schema `main` as it is the default schema_.

Replace `<column_name>` with `*` to select all columns from a table:

```sql
SELECT * FROM resale_flat_prices_2017;
```

> Select any 3 columns from the table.

### Operators and functions

Operators and functions are used to perform operations on data. They are used in the `SELECT` statement. The result of the operation is returned as a new column.

#### Operators

Operators are used to perform mathematical operations on data. The following are some commonly used operators:

| Operator | Description    |
| -------- | -------------- |
| `+`      | Addition       |
| `-`      | Subtraction    |
| `*`      | Multiplication |
| `/`      | Division       |
| `%`      | Modulo         |

If we want to get the resale price in thousands, we can use the `/` operator to divide the resale price by 1000:

```sql
SELECT resale_price / 1000 FROM resale_flat_prices_2017;
```

Use the `AS` keyword to rename the column:

```sql
SELECT resale_price / 1000 AS resale_price_thousands FROM resale_flat_prices_2017;
```

There are also non-mathematical operators which we will explore later.

#### Functions

Functions are used to perform operations on data. The following are some example functions:

| Function   | Description                                                              |
| ---------- | ------------------------------------------------------------------------ |
| `ABS()`    | Returns the absolute value of a number.                                  |
| `ROUND()`  | Returns a numeric value rounded to a specified number of decimal places. |
| `LOWER()`  | Returns a string in lowercase.                                           |
| `UPPER()`  | Returns a string in uppercase.                                           |
| `LENGTH()` | Returns the length (number of characters) of a string.                   |
| `TRIM()`   | Removes leading and trailing spaces from a string.                       |
| `CONCAT()` | Concatenates two or more strings.                                        |

Example:

```sql
SELECT ABS(resale_price) FROM resale_flat_prices_2017;
```

> Select column town as lowercase
>
> Concatenate block and street_name and return as a new column named address

### Filters

Filters are used to filter data based on a condition. The `WHERE` clause is used to filter data in a `SELECT` statement. It commonly uses comparison operators to compare values.

The following are some commonly used operators:

| Operator | Description      |
| -------- | ---------------- |
| `=`      | Equal            |
| `<>`     | Not equal        |
| `>`      | Greater          |
| `>=`     | Greater or equal |
| `<`      | Less             |
| `<=`     | Less or equal    |
| `AND`    | Logical AND      |
| `OR`     | Logical OR       |
| `NOT`    | Logical NOT      |

Example:

```sql
SELECT * FROM resale_flat_prices_2017 WHERE town = 'BUKIT MERAH';
```

We can introduce line breaks (new lines) to make the query more readable:

```sql
SELECT *
FROM resale_flat_prices_2017
WHERE town = 'BUKIT MERAH';
```

> Select flats with floor area greater than 100 sqm
>
> Select flats with resale price between 400,000 and 500,000
>
> Select flats with lease commence date later than year 2000 and floor area greater than 100 sqm

### Sorting

The `ORDER BY` clause is used to sort data by a column in a `SELECT` statement. It can sort data in ascending or descending order. The default is ascending order. It can also sort by multiple columns.

Example:

```sql
SELECT * FROM resale_flat_prices_2017 ORDER BY lease_commence_date;
```

```sql
SELECT * FROM resale_flat_prices_2017 ORDER BY resale_price DESC;
```

Return flats with the most recent lease commence date and highest to lowest resale price:

```sql
SELECT *
FROM resale_flat_prices_2017
ORDER BY lease_commence_date DESC, resale_price DESC;
```

> Select flats from highest to lowest resale price in Punggol

### Aggregate functions

Aggregate functions are used to perform calculations on a set of values and return a single value. The following are some commonly used aggregate functions:

| Function  | Description                            |
| --------- | -------------------------------------- |
| `COUNT()` | Returns the number of rows in a table. |
| `SUM()`   | Returns the sum of values in a column. |
| `AVG()`   | Returns the average value of a column. |
| `MIN()`   | Returns the minimum value in a column. |
| `MAX()`   | Returns the maximum value in a column. |
| `FIRST()` | Returns the first value in a column.   |
| `LAST()`  | Returns the last value in a column.    |

Example:

```sql
SELECT COUNT(*) FROM resale_flat_prices_2017;
```

```sql
SELECT AVG(resale_price) FROM resale_flat_prices_2017;
```

```sql
SELECT MAX(resale_price) FROM resale_flat_prices_2017;
```

> Select the average resale price of flats in Bishan
>
> Select the total resale value (price) of flats in Tampines

### Group by

The `GROUP BY` clause is used to group rows that have the same values into summary rows. It is often used with aggregate functions to perform calculations on each group. The `GROUP BY` clause comes after the `WHERE` clause and before the `ORDER BY` clause.

Average resale price of flats in each town:

```sql
SELECT town, AVG(resale_price)
FROM resale_flat_prices_2017
GROUP BY town;
```

You can also group by multiple columns:

```sql
SELECT town, lease_commence_date, AVG(resale_price)
FROM resale_flat_prices_2017
GROUP BY town, lease_commence_date;
```

You can replace the `town, lease_commence_date` after the `GROUP BY` with `1, 2` to group by the first and second columns:

```sql
SELECT town, lease_commence_date, AVG(resale_price)
FROM resale_flat_prices_2017
GROUP BY 1, 2;
```

Combined with sorting:

```sql
SELECT town, lease_commence_date, AVG(resale_price)
FROM resale_flat_prices_2017
GROUP BY town, lease_commence_date
ORDER BY town, lease_commence_date DESC;
```

> Select the average resale price by flat type
>
> Select the average resale price by flat type and flat model
>
> Select the average resale price by town and lease commence date only for lease commence dates after year 2010 and sort by town (descending) and lease commence date (descending)

### Having

The `HAVING` clause is used to filter groups in a `GROUP BY` clause. It is similar to the `WHERE` clause but it is used with aggregate functions. The `HAVING` clause comes after the `GROUP BY` clause and before the `ORDER BY` clause.

Average resale price of flats in each town with average resale price greater than 500,000:

```sql
SELECT town, AVG(resale_price)
FROM resale_flat_prices_2017
GROUP BY town
HAVING AVG(resale_price) > 500000;
```

Difference between `WHERE` and `HAVING`:

```sql
SELECT town, AVG(resale_price)
FROM resale_flat_prices_2017
WHERE resale_price > 500000
GROUP BY town;
```

```sql
SELECT town, AVG(resale_price)
FROM resale_flat_prices_2017
GROUP BY town
HAVING AVG(resale_price) > 500000;
```

`HAVING` can only be used on columns that appear in the `SELECT` clause or columns that are used in aggregate functions.

> Select the maximum resale price by town only for town with maximum resale price greater than 1,000,000

### Advanced operators and functions

#### `IN`

The `IN` operator is used to specify multiple values in a `WHERE` clause. It is similar to using multiple `OR` operators.

```sql
SELECT * FROM resale_flat_prices_2017 WHERE town IN ('BUKIT MERAH', 'BUKIT TIMAH');
```

#### `BETWEEN`

The `BETWEEN` operator is used to specify a range of values in a `WHERE` clause. It is similar to using `>=` and `<=` operators.

```sql
SELECT * FROM resale_flat_prices_2017 WHERE resale_price BETWEEN 400000 AND 500000;
```

#### `LIKE`

The `LIKE` operator is used to specify a pattern in a `WHERE` clause. It is used with the `%` wildcard to match zero or more characters and the `_` wildcard to match a single character.

```sql
SELECT * FROM resale_flat_prices_2017 WHERE town LIKE 'B%';
```

#### `DISTINCT`

The `DISTINCT` operator is used to return unique (remove duplicated) values in a `SELECT` statement.

```sql
SELECT DISTINCT town FROM resale_flat_prices_2017;
```

> Return the unique flat types and flat models

#### `CASE`

The `CASE` expression is used to evaluate a list of conditions and return a value. It is similar to the `IF` statement in programming languages. It starts with the `CASE` keyword followed by the `WHEN` keyword and ends with the `END` keyword.

```sql
SELECT town, resale_price,
CASE WHEN resale_price > 500000 THEN 'High' ELSE 'Low' END AS price_level
FROM resale_flat_prices_2017;
```

You can also use multiple `WHEN` clauses:

```sql
SELECT town, resale_price,
CASE WHEN resale_price > 1000000 THEN 'High' WHEN resale_price > 500000 THEN 'Medium' ELSE 'Low' END AS price_level
FROM resale_flat_prices_2017;
```

> Return the records with a new column `flat_size` with values `Small` if flat type is `1-3 ROOM`, `Medium` if flat type is `4 ROOM` and `Large` if flat type is `5 ROOM`, `EXECUTIVE` or `MULTI-GENERATION`

#### `CAST`

The `CAST` function is used to convert a value from one data type to another data type.

```sql
SELECT town, resale_price, CAST(resale_price AS INTEGER) FROM resale_flat_prices_2017;
```

or `::` can be used instead of `CAST`:

```sql
SELECT town, resale_price, resale_price::INTEGER FROM resale_flat_prices_2017;
```

#### Date functions

We can convert the `month` column from varchar to `date` using `CAST`. We can concatenate the day of the month to the month.

```sql
SELECT *, CONCAT(month, '-01')::date AS transaction_date FROM resale_flat_prices_2017;
```

We can use our knowledge of DDL to add that as a new column to the table:

```sql
ALTER TABLE resale_flat_prices_2017 ADD COLUMN transaction_date date;
```

```sql
UPDATE resale_flat_prices_2017 SET transaction_date = CONCAT(month, '-01')::date;
```

We can extract the year of transaction from the transaction date:

```sql
SELECT *, date_part('year',transaction_date) AS transaction_year FROM resale_flat_prices_2017;
```
