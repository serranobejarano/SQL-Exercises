## 11) Error Handling & Transactions

#### 11.1) Logging Errors
```SQL
DECLARE @OrderID int = 0

-- Declare a custom error if the specified order doesn't exist
DECLARE @error varchar(25) = 'Order #' + cast(@OrderID as varchar) + ' does not exist';
IF NOT EXISTS (SELECT * FROM SalesLT.SalesOrderHeader WHERE SalesOrderID = @OrderID)
BEGIN
  THROW 50001, @error, 0;
END
ELSE
BEGIN
  DELETE FROM SalesLT.SalesOrderDetail WHERE SalesOrderID = @OrderID;
  DELETE FROM SalesLT.SalesOrderHeader WHERE SalesOrderID = @OrderID;
END
```

#### 11.2) Logging Errors (2)
```SQL
DECLARE @OrderID int = 71774
DECLARE @error varchar(25) = 'Order #' + cast(@OrderID as varchar) + ' does not exist';

-- Wrap IF ELSE in a TRY block
BEGIN TRY
  IF NOT EXISTS (SELECT * FROM SalesLT.SalesOrderHeader WHERE SalesOrderID = @OrderID)
  BEGIN
    THROW 50001, @error, 0
  END
  ELSE
  BEGIN
    DELETE FROM SalesLT.SalesOrderDetail WHERE SalesOrderID = @OrderID;
    DELETE FROM SalesLT.SalesOrderHeader WHERE SalesOrderID = @OrderID;
  END
END TRY

-- Add a CATCH block to print out the error
BEGIN CATCH
  PRINT ERROR_MESSAGE();
END CATCH
```

#### 11.3) Ensuring Data Consistency
```SQL
DECLARE @OrderID int = 0
DECLARE @error varchar(25) = 'Order #' + cast(@OrderID as varchar) + ' does not exist';

BEGIN TRY
  IF NOT EXISTS (SELECT * FROM SalesLT.SalesOrderHeader WHERE SalesOrderID = @OrderID)
  BEGIN
    THROW 50001, @error, 0
  END
  ELSE
  BEGIN
    BEGIN TRANSACTION
    DELETE FROM SalesLT.SalesOrderDetail
    WHERE SalesOrderID = @OrderID;
    DELETE FROM SalesLT.SalesOrderHeader
    WHERE SalesOrderID = @OrderID;
    COMMIT TRANSACTION
  END
END TRY

BEGIN CATCH
  IF @@TRANCOUNT > 0
  BEGIN
    ROLLBACK TRANSACTION;
  END
  ELSE
  BEGIN
    PRINT ERROR_MESSAGE();
  END
END CATCH
```
