# DBF-Training

## 1-Queries

- MsSQL 2019 Database wideworldimporters example, you can find it here: <https://github.com/Microsoft/sql-server-samples/releases/tag/wide-world-importers-v1.0>


Check the restored database:

![](_attachments/Pasted%20image%2020231017172731.png)
### Stored Procedure

Query scripts: 
```
-- Switch Database
USE WideWorldImporters;

-- Check sp existed or not

IF OBJECT_ID('sp_SelectTop10_Orders', 'P') IS NOT NULL
BEGIN
    DROP PROCEDURE sp_SelectTop10_Orders;
END;

-- Create a stored procedure to select the first 10 rows from the Sales.Orders table

CREATE PROCEDURE sp_SelectTop10_Orders
AS
BEGIN
    -- Start the procedure
    SELECT TOP 10 *
    FROM Sales.Orders; -- Use "Orders" instead of "Order"
    
    -- End the procedure
END;

-- Execute the Sp

EXEC sp_SelectTop10_Orders;
```

You should be able to find the newly created stored_procedure under schema dbo.
![](_attachments/Pasted%20image%2020231017173250.png)

Or you can run the query manually:
```
-- The command replaced by stored procedure
SELECT TOP 10 * FROM Sales.Orders o ;
```

### Privilege Operations

Here are some common privileged operations in MSSQL:

1. **Creating, altering, and dropping databases**: Permissions to create or remove an entire database.
    
2. **Creating, altering, and dropping tables or views**: Permissions related to the structure of individual tables or views within a database.
    
3. **Backup and restore**: The ability to backup a database or restore it from a backup is a privileged operation.
    
4. **Assigning permissions**: Only users with the necessary privileges can grant or revoke permissions to other users or roles. This includes `GRANT`, `DENY`, and `REVOKE` commands.
    
5. **Creating or modifying stored procedures and functions**: Permissions related to the creation, modification, or deletion of stored procedures and functions.
    
6. **Schema creation and management**: Permissions to create, alter, or drop schemas.
    
7. **Creating or altering indexes and constraints**: Operations that can impact the database's performance and data integrity.
    
8. **Setting up replication**: The ability to set up, modify, or remove database replication.
    
9. **Management of roles and logins**: The ability to create, modify, or remove server or database roles and logins.
    
10. **Monitoring and tuning**: Using tools or commands to monitor the database's performance, diagnose issues, or optimize its operation.
    
11. **Shutting down the server**: The ability to stop the SQL Server instance.

Query to drop table, for instance:

```
-- DROP TABLE NewTable
USE WideWorldImporters;

CREATE TABLE dbo.NewTable (
    UserID INT IDENTITY(1,1) PRIMARY KEY,
    UserName NVARCHAR(50),
    Age INT
);

DROP TABLE dbo.NewTable; 
```

### Views

create example table users:
```
-- Insert some sample data
INSERT INTO Users (UserName, Age) VALUES 
('John', 28),
('Jane', 32),
('Doe', 30),
('Alice', 29),
('Bob', 35);
```

create views:

```
CREATE VIEW UserInformationView AS
SELECT 
    UserID,
    UserName,
    Age,
    (CASE WHEN Age BETWEEN 0 AND 10 THEN 'Child' 
          WHEN Age BETWEEN 11 AND 20 THEN 'Teenager' 
          WHEN Age BETWEEN 21 AND 30 THEN 'Young Adult' 
          WHEN Age BETWEEN 31 AND 40 THEN 'Adult' 
          ELSE 'Senior' END) AS AgeGroup,
    (CASE WHEN Age % 2 = 0 THEN 'Even Age' 
          ELSE 'Odd Age' END) AS AgeParity,
    (CASE WHEN LEN(UserName) % 2 = 0 THEN 'Even Name Length' 
          ELSE 'Odd Name Length' END) AS NameParity,
    (CASE WHEN Age < 30 THEN 'Below 30' 
          WHEN Age >= 30 AND Age < 40 THEN 'Between 30 and 40' 
          ELSE 'Above 40' END) AS AgeBracket,
    (CASE WHEN Age > 30 AND LEN(UserName) > 3 THEN 'Age > 30 AND Name Length > 3' 
          ELSE 'Other' END) AS ComplexCriterion
FROM Users 
WHERE Age > 25 
AND UserName LIKE 'J%' 
AND (Age % 2 = 0 OR LEN(UserName) % 2 = 0) 
AND UserID IN (SELECT UserID FROM Users WHERE Age BETWEEN 28 AND 35);
```

![](_attachments/Pasted%20image%2020231017175728.png)

Check the view result:

```
SELECT * FROM UserInformationView
```

### City_name as example

```
-- Create the world_city table with schema dbo
USE WideWorldImporters;

CREATE TABLE dbo.world_city (
    city_id INT PRIMARY KEY IDENTITY(1,1), -- Automatically incrementing unique ID
    city_name NVARCHAR(255) NOT NULL
);

-- Insert 50 famous cities into world_city table
INSERT INTO dbo.world_city (city_name) VALUES
('New York'),
('London'),
('Paris'),
('Tokyo'),
('Los Angeles'),
('Chicago'),
('Houston'),
('Miami'),
('San Francisco'),
('Toronto'),
('Sydney'),
('Melbourne'),
('Rio de Janeiro'),
('Sao Paulo'),
('Mexico City'),
('Shanghai'),
('Beijing'),
('Mumbai'),
('Bangkok'),
('Singapore'),
('Hong Kong'),
('Seoul'),
('Amsterdam'),
('Dubai'),
('Cairo'),
('Johannesburg'),
('Moscow'),
('Berlin'),
('Madrid'),
('Rome'),
('Istanbul'),
('Lisbon'),
('Copenhagen'),
('Vienna'),
('Budapest'),
('Prague'),
('Warsaw'),
('Buenos Aires'),
('Lima'),
('Bogota'),
('Montreal'),
('Vancouver'),
('Jakarta'),
('Manila'),
('Kuala Lumpur'),
('Nairobi'),
('Lagos'),
('Karachi'),
('Dhaka');
```

