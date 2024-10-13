In SQL Server, **window functions** allow you to perform calculations across a set of rows related to the current row, without the need to group rows using a `GROUP BY` clause. These functions are powerful for analytical purposes such as ranking, aggregating, and comparing rows within a defined partition or window. Window functions are categorized mainly into **aggregate functions**, **ranking functions**, and **value functions**. Below is a detailed explanation of each category and the key functions in SQL Server.

---

### 1. **Aggregate Window Functions**
These functions perform aggregate calculations over a set of rows (a "window") and return a single value for each row within that window.

- **SUM()**: Returns the sum of values in the window.
    ```sql
    SELECT EmployeeID, Salary, SUM(Salary) OVER (PARTITION BY DepartmentID ORDER BY HireDate) AS RunningTotal
    FROM Employees;
    ```
    - This calculates the running total of salaries for each department.

- **AVG()**: Returns the average of the values in the window.
    ```sql
    SELECT EmployeeID, Salary, AVG(Salary) OVER (PARTITION BY DepartmentID) AS AvgSalary
    FROM Employees;
    ```
    - This returns the average salary per department.

- **COUNT()**: Returns the count of rows in the window.
    ```sql
    SELECT EmployeeID, COUNT(*) OVER (PARTITION BY DepartmentID) AS EmployeeCount
    FROM Employees;
    ```
    - This counts the number of employees in each department.

- **MAX()**: Returns the maximum value in the window.
    ```sql
    SELECT EmployeeID, MAX(Salary) OVER (PARTITION BY DepartmentID) AS MaxSalary
    FROM Employees;
    ```
    - This finds the highest salary in each department.

- **MIN()**: Returns the minimum value in the window.
    ```sql
    SELECT EmployeeID, MIN(Salary) OVER (PARTITION BY DepartmentID) AS MinSalary
    FROM Employees;
    ```
    - This returns the lowest salary in each department.

---

### 2. **Ranking Functions**
Ranking functions assign a rank to each row within a partition based on the specified ordering.

- **ROW_NUMBER()**: Assigns a unique sequential integer to rows, starting from 1, within the partition.
    ```sql
    SELECT EmployeeID, Salary, ROW_NUMBER() OVER (PARTITION BY DepartmentID ORDER BY Salary DESC) AS RowNum
    FROM Employees;
    ```
    - This ranks employees within each department by their salary in descending order.

- **RANK()**: Assigns a rank to each row in the partition. Rows with equal values receive the same rank, but the next rank will skip by the number of duplicates.
    ```sql
    SELECT EmployeeID, Salary, RANK() OVER (PARTITION BY DepartmentID ORDER BY Salary DESC) AS Rank
    FROM Employees;
    ```
    - Employees with the same salary will share the same rank, but the next employee will have a rank that skips accordingly.

- **DENSE_RANK()**: Similar to `RANK()`, but it assigns consecutive ranks without gaps.
    ```sql
    SELECT EmployeeID, Salary, DENSE_RANK() OVER (PARTITION BY DepartmentID ORDER BY Salary DESC) AS DenseRank
    FROM Employees;
    ```
    - This ensures that the next rank is assigned consecutively even if there are ties.

- **NTILE(n)**: Divides the result set into `n` roughly equal parts and assigns a number from 1 to `n` to each row.
    ```sql
    SELECT EmployeeID, Salary, NTILE(4) OVER (ORDER BY Salary DESC) AS Quartile
    FROM Employees;
    ```
    - This divides the employees into 4 quartiles based on their salary.

---

### 3. **Value Functions**
These functions provide access to data from other rows within the window, either previous, next, or first/last within the window frame.

- **LEAD()**: Returns the value of the following row in the result set. You can specify an offset to look further ahead.
    ```sql
    SELECT EmployeeID, Salary, LEAD(Salary, 1) OVER (ORDER BY HireDate) AS NextSalary
    FROM Employees;
    ```
    - This retrieves the salary of the next employee by hire date.

- **LAG()**: Returns the value of the preceding row in the result set. You can also specify an offset.
    ```sql
    SELECT EmployeeID, Salary, LAG(Salary, 1) OVER (ORDER BY HireDate) AS PrevSalary
    FROM Employees;
    ```
    - This retrieves the salary of the previous employee by hire date.

- **FIRST_VALUE()**: Returns the first value in the window frame based on the ordering.
    ```sql
    SELECT EmployeeID, Salary, FIRST_VALUE(Salary) OVER (PARTITION BY DepartmentID ORDER BY HireDate) AS FirstSalary
    FROM Employees;
    ```
    - This returns the first salary within each department based on the hire date.

- **LAST_VALUE()**: Returns the last value in the window frame based on the ordering.
    ```sql
    SELECT EmployeeID, Salary, LAST_VALUE(Salary) OVER (PARTITION BY DepartmentID ORDER BY HireDate ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING) AS LastSalary
    FROM Employees;
    ```
    - This returns the last salary within each department, but the window frame must be fully defined for correct behavior.

---

### 4. **Window Frame Definition**
You can control how many rows to include in the window by defining a frame using the `ROWS` or `RANGE` clauses. 

- **ROWS**: Defines the window frame based on the physical number of rows.
    ```sql
    SELECT EmployeeID, Salary, SUM(Salary) OVER (ORDER BY HireDate ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING) AS MovingTotal
    FROM Employees;
    ```
    - This computes a moving total that includes the current row, the previous row, and the next row.

- **RANGE**: Defines the window frame based on logical conditions (e.g., range of values).
    ```sql
    SELECT EmployeeID, Salary, SUM(Salary) OVER (ORDER BY HireDate RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS CumulativeTotal
    FROM Employees;
    ```
    - This calculates the cumulative total salary up to the current row, including all prior rows in the same logical range.

---

### Summary of Window Functions in SQL Server:
- **Aggregate Functions**: Perform calculations over a set of rows (`SUM`, `AVG`, `COUNT`, `MAX`, `MIN`).
- **Ranking Functions**: Provide row rankings within a partition (`ROW_NUMBER()`, `RANK()`, `DENSE_RANK()`, `NTILE()`).
- **Value Functions**: Allow access to the values from other rows in the window (`LEAD()`, `LAG()`, `FIRST_VALUE()`, `LAST_VALUE()`).
- **Window Frame Definition**: Defines which rows are included in the window calculation (using `ROWS` or `RANGE` clauses).

These functions are powerful tools for advanced analytics and reporting in SQL Server, allowing for flexible calculations across partitions of data.
