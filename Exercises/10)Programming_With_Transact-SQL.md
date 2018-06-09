## 10) Programming with Transact-SQL

#### 10.1) Writing a Script to Insert an Order Header
```SQL
DECLARE @OrderDate datetime = GETDATE();
DECLARE @DueDate datetime = DATEADD(dd, 7, GETDATE());
DECLARE @CustomerID int = 1;
INSERT INTO SalesLT.SalesOrderHeader (OrderDate, DueDate, CustomerID, ShipMethod)
VALUES(@OrderDate, @DueDate, @CustomerID, 'CARGO TRANSPORT 5');
PRINT SCOPE_IDENTITY();
```

#### 10.2) Extend Script to Insert an Order Detail
```SQL
-- Code from previous exercise
DECLARE @OrderDate datetime = GETDATE();
DECLARE @DueDate datetime = DATEADD(dd, 7, GETDATE());
DECLARE @CustomerID int = 1;
INSERT INTO SalesLT.SalesOrderHeader (OrderDate, DueDate, CustomerID, ShipMethod)
VALUES (@OrderDate, @DueDate, @CustomerID, 'CARGO TRANSPORT 5');
DECLARE @OrderID int = SCOPE_IDENTITY();

-- Additional script to complete
DECLARE @ProductID int = 760;
DECLARE @Quantity int = 1;
DECLARE @UnitPrice money = 782.99;

IF EXISTS (SELECT * FROM SalesLT.SalesOrderDetail WHERE SalesOrderID = SalesLT.SalesOrderHeader.OrderID)
BEGIN
  INSERT INTO SalesLT.SalesOrderDetail (SalesOrderID, OrderQty, ProductID, UnitPrice)
  VALUES (@OrderID, @Quantity, @ProductID, @UnitPrice)
END
ELSE
BEGIN
	PRINT 'The order does not exist'
END
```

#### 10.3) Updating Bike Prices
```SQL
DECLARE @MarketAverage money = 2000;
DECLARE @MarketMax money = 5000;
DECLARE @AWMax money;
DECLARE @AWAverage money;

SELECT @AWAverage = AVG(SalesLT.Product.ListPrice), @AWMax = MAX(SalesLT.Product.ListPrice)
FROM SalesLT.Product
WHERE ProductCategoryID IN
  (SELECT DISTINCT ProductCategoryID
  FROM SalesLT.vGetAllCategories
  WHERE ParentProductCategoryName = 'Bikes');

WHILE @AWAverage < @MarketAverage
BEGIN
  UPDATE SalesLT.Product
  SET ListPrice = ListPrice * 1.1
  WHERE ProductCategoryID IN
    (SELECT DISTINCT ProductCategoryID
      FROM SalesLT.vGetAllCategories
      WHERE ParentProductCategoryName = 'Bikes');

    SELECT @AWAverage = AVG(SalesLT.Product.ListPrice), @AWMax = MAX(SalesLT.Product.ListPrice)
    FROM SalesLT.Product
    WHERE ProductCategoryID IN
      (SELECT DISTINCT ProductCategoryID
        FROM SalesLT.vGetAllCategories
        WHERE ParentProductCategoryName = 'Bikes');

    IF @AWMax >= @MarketMax
      EXIT
    ELSE
      CONTINUE
END

PRINT 'New average bike price:' + CONVERT(VARCHAR, @AWAverage);
PRINT 'New maximum bike price:' + CONVERT(VARCHAR, @AWMax);
```
