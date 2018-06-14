# Examples of Views<a name="views-examples"></a>

To show the syntax of the view query, use [SHOW CREATE VIEW](show-create-view.md)\.

**Example Example 1**  
Consider the following two tables: a table `employees` with two columns, `id` and `name`, and a table `salaries`, with two columns, `id` and `salary`\.   
In this example, we create a view named `name_salary` as a `SELECT` query that obtains a list of IDs mapped to salaries from the tables `employees` and `salaries`:  

```
CREATE VIEW name_salary AS
SELECT
 employees.name, 
 salaries.salary 
FROM employees, salaries 
WHERE employees.id = salaries.id
```

**Example Example 2**  
In the following example, we create a view named `view1` that enables you to hide more complex query syntax\.   
This view runs on top of two tables, `table1` and `table2`, where each table is a different `SELECT` query\. The view selects all columns from `table1` and joins the results with `table2`\. The join is based on column `a` that is present in both tables\.  

```
CREATE VIEW view1 AS
WITH
  table1 AS (
         SELECT a, 
         MAX(b) AS b 
         FROM x 
         GROUP BY a
         ),
  table2 AS (
         SELECT a, 
         AVG(d) AS d 
         FROM y 
         GROUP BY a)
SELECT table1.*, table2.*
FROM table1
JOIN table2 
ON table1.a = table2.a;
```