In SQL Server, **window functions** are used to perform calculations across a set of table rows related to the current row without the need for a `GROUP BY` clause. These functions operate over a "window" or subset of rows, providing great flexibility for performing calculations like running totals, rankings, moving averages, and more.

Here are the main categories of window functions in SQL Server:

### 1. **Aggregate Functions**  
These are used to calculate aggregate values over a defined window of rows.

- **SUM()**: Calculates the sum of a set of values within the window.
    ```sql
    SELECT SUM(Salary) OVER (PARTITION BY DepartmentID ORDER BY HireDate) AS RunningTotal
    FROM Employees;
    ```
- **AVG()**: Calculates the average value.
    ```sql
    SELECT AVG(Salary) OVER (PARTITION BY DepartmentID) AS AvgSalary
    FROM Employees;
    ```
- **COUNT()**: Counts the number of rows in the window.
    ```sql
    SELECT COUNT(*) OVER (PARTITION BY DepartmentID) AS EmployeeCount
    FROM Employees;
    ```
- **MAX()**: Returns the maximum value within the window.
    ```sql
    SELECT MAX(Salary) OVER (PARTITION BY DepartmentID) AS MaxSalary
    FROM Employees;
    ```
- **MIN()**: Returns the minimum value.
    ```sql
    SELECT MIN(Salary) OVER (PARTITION BY DepartmentID) AS MinSalary
    FROM Employees;
    ```

### 2. **Ranking Functions**  
These assign a rank to each row in a partition or result set.

- **ROW_NUMBER()**: Assigns a unique sequential integer to rows within a partition of a result set.
    ```sql
    SELECT ROW_NUMBER() OVER (PARTITION BY DepartmentID ORDER BY Salary DESC) AS RowNum
    FROM Employees;
    ```
- **RANK()**: Assigns a rank to each row, with gaps in ranking when there are ties.
    ```sql
    SELECT RANK() OVER (PARTITION BY DepartmentID ORDER BY Salary DESC) AS Rank
    FROM Employees;
    ```
- **DENSE_RANK()**: Similar to `RANK()`, but without gaps in the ranking sequence when there are ties.
    ```sql
    SELECT DENSE_RANK() OVER (PARTITION BY DepartmentID ORDER BY Salary DESC) AS DenseRank
    FROM Employees;
    ```
- **NTILE(n)**: Distributes the rows into a specified number (`n`) of approximately equal-sized groups.
    ```sql
    SELECT NTILE(4) OVER (ORDER BY Salary DESC) AS Quartile
    FROM Employees;
    ```

### 3. **Value Functions**  
These return specific values from the rows in the window.

- **LEAD()**: Provides access to the next row in the result set (based on the order specified).
    ```sql
    SELECT Salary, LEAD(Salary) OVER (ORDER BY HireDate) AS NextSalary
    FROM Employees;
    ```
- **LAG()**: Provides access to the previous row in the result set.
    ```sql
    SELECT Salary, LAG(Salary) OVER (ORDER BY HireDate) AS PrevSalary
    FROM Employees;
    ```
- **FIRST_VALUE()**: Returns the first value in the window.
    ```sql
    SELECT FIRST_VALUE(Salary) OVER (PARTITION BY DepartmentID ORDER BY HireDate) AS FirstSalary
    FROM Employees;
    ```
- **LAST_VALUE()**: Returns the last value in the window.
    ```sql
    SELECT LAST_VALUE(Salary) OVER (PARTITION BY DepartmentID ORDER BY HireDate ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING) AS LastSalary
    FROM Employees;
    ```

### 4. **Window Frame Definition**  
When using window functions, you can define a "window frame" to limit the rows considered for the calculation. The frame is defined by the `ROWS` or `RANGE` clause:

- **ROWS**: Limits the rows based on their physical position.
    ```sql
    SUM(Salary) OVER (ORDER BY HireDate ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING)
    ```
    - This calculates the sum of the current row, the row before, and the row after.

- **RANGE**: Limits the rows based on logical conditions (e.g., based on values).
    ```sql
    SUM(Salary) OVER (ORDER BY HireDate RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW)
    ```
    - This calculates the sum of all rows up to and including the current row, starting from the earliest row.

### Example Usage:

```sql
SELECT 
    EmployeeID, 
    Salary, 
    SUM(Salary) OVER (PARTITION BY DepartmentID ORDER BY HireDate ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS RunningTotal,
    ROW_NUMBER() OVER (PARTITION BY DepartmentID ORDER BY Salary DESC) AS Rank
FROM Employees;
```

In this example:
- **SUM()** provides a running total of salaries for each department.
- **ROW_NUMBER()** ranks employees by their salary within each department.

### Summary
Window functions in SQL Server are powerful tools for advanced data analytics. They allow you to:
- Perform calculations like aggregates (SUM, AVG) over a specified window.
- Rank rows using functions like `ROW_NUMBER()`, `RANK()`, and `DENSE_RANK()`.
- Access values from previous or following rows with `LAG()` and `LEAD()`.
- Control the scope of the window using the `ROWS` and `RANGE` clauses. 

These functions offer flexibility and efficiency, making them essential for various types of analytical queries.
