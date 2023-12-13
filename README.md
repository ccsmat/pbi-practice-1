# Use a SQL example for practice
This example uses two correlated subqueries to find the names of employees who sold a particular product.

![image](https://github.com/ccsmat/pbi-practice-1/assets/29472739/56266327-cc62-4440-8a2b-1f0fd18ce237)
https://learn.microsoft.com/en-us/sql/t-sql/queries/select-examples-transact-sql?view=sql-server-ver15#e-use-correlated-subqueries

The query:

```sql
SELECT DISTINCT pp.LastName,
    pp.FirstName
FROM Person.Person pp -- table 5
INNER JOIN HumanResources.Employee e -- table 4
    ON e.BusinessEntityID = pp.BusinessEntityID -- get employee list
WHERE pp.BusinessEntityID IN (
    SELECT SalesPersonID
    FROM Sales.SalesOrderHeader -- table 3
    WHERE SalesOrderID IN (
        SELECT SalesOrderID
        FROM Sales.SalesOrderDetail -- table 2
        WHERE ProductID IN (
            SELECT ProductID
            FROM Production.Product p -- table 1
            WHERE ProductNumber = 'BK-M68B-42'
        )
    )
);
```

The result of this query.

![image](https://github.com/ccsmat/pbi-practice-1/assets/29472739/2beb2d08-effb-4a84-9689-174888121477)

# How to achieve the same result in Power BI?

Based on the SQL query, 5 tables are involved. Complex relationships are built between these tables in the current model so I decided to use power query for data transformation rather than using DAX.

![image](https://github.com/ccsmat/pbi-practice-1/assets/29472739/c886b76e-ab83-4d8b-bd33-bbe20d6f887d)

In Power Query, we can use the Person.Person table and HumanResources.Employee to create a GetCurrentEmployeeID table. This table will be used as a dictionary.

## GetCurrentEmployeeID table

![image](https://github.com/ccsmat/pbi-practice-1/assets/29472739/35a386de-ba1a-4faa-8595-5a7bc6ebc16a)

## GetSalesPersonID table

To create this table, follow the steps:

1. Reference a Production.Product table and filter ProductNumber with BK-M68B-42.

2. First merge and expand: Merge SalesOrderDetail table using left outer and expand the SalesOrderID column to rows.

![image](https://github.com/ccsmat/pbi-practice-1/assets/29472739/1db1edb1-f590-4c0d-8ca5-843011e854ab)

3. Second merge and expand: Merge SalesOrderHeader table using left outer and expand the SalesPersonID column to rows.

4. Filter out the nulls in the SalesPersonID

![image](https://github.com/ccsmat/pbi-practice-1/assets/29472739/e0deef9b-b55f-40d9-8668-7aef50b0c40a)

## result table

Merge GetCurrentEmployeeID table and GetSalesPersonID table with inner join.

![image](https://github.com/ccsmat/pbi-practice-1/assets/29472739/a838af7d-e03c-440a-beef-7bb085a5093c)


