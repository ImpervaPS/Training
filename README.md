# Training
Public Learning Material

- MsSQL 2019 Database wideworldimporters example, you can find it here: <https://github.com/Microsoft/sql-server-samples/releases/tag/wide-world-importers-v1.0>


Check the restored database:

![](_attachments/Pasted%20image%2020231017172731.png)

Query scripts:

1-Stored Procedure

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
