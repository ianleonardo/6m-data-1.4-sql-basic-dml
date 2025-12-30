# **Project Assignment: "The Property Analyst"**

**Goal:** Identify the most "undervalued" town.

1. Find the average resale\_price per town.  
2. Filter for towns where the average price is less than $450,000.  
3. Within those towns, find the top 5 largest flats (by floor\_area\_sqm) currently available.  
4. **Level Up:** Create a new column called price\_per\_sqm and find which town has the lowest average price per square meter.


---

**Post-class Materials**

* **Deep Dive:** [DuckDB Documentation on Aggregate Functions](https://duckdb.org/docs/sql/aggregates).  
* **Optional Reading:** "SQL Performance Tuning for Beginners" – Why SELECT \* is often avoided in production.


---

Write the SQL DML statements for the following questions.

## Instructions

Paste the answer as SQL in the answer code section below each question.

### Question 1

Select the minimum and maximum price per sqm of all the flats.

```sql

```

### Question 2

Select the average price per sqm for flats in each town.

```sql

```

### Question 3

Categorize flats into price ranges and count how many flats fall into each category:

- Under $400,000: 'Budget'
- $400,000 to $700,000: 'Mid-Range'
- Above $700,000: 'Premium'
  Show the counts in descending order.

```sql

```

### Question 4

Count the number of flats sold in each town during the first quarter of 2017 (January to March).

```sql

```
