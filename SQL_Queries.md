# Querying with SQL - Exercises
## 1) Introduction to Transact-SQL

#### 1.1) Retrieving Customer Data

 ```SQL
 -- select all columns
 -- from the SalesLT.Customer table
SELECT * FROM SalesLT.Customer;
 ```

#### 1.2) Create List of Customer Contacts

```SQL
-- select the Title, FirstName, MiddleName, LastName and Suffix columns
-- from the Customer table
SELECT Title, FirstName, MiddleName, LastName, Suffix
FROM SalesLT.Customer;
```

#### 1.3) Create List of Customer Contacts (2)

```SQL
SELECT SalesPerson, Title + ' ' + LastName AS CustomerName, Phone
FROM SalesLT.Customer;
```

#### 1.4) Retrieving Customer and Sales Data

```SQL
-- cast the CustomerID column to a VARCHAR and concatenate with the CompanyName column
SELECT CAST(CustomerID AS VARCHAR) + ': ' + CompanyName AS CustomerCompany
FROM SalesLT.Customer;
```

#### 1.5) Retrieving Customer and Sales Data (2)

```SQL
SELECT SalesOrderNumber + ' (' + STR( RevisionNumber, 1) + ')' AS OrderRevision,
	   CONVERT(nvarchar(30), OrderDate, 102) AS OrderDate
FROM SalesLT.SalesOrderHeader;
```

#### 1.6) Retrieving Customer Contact Names

```SQL
-- use ISNULL to check for middle names and concatenate with FirstName and LastName
SELECT FirstName + ' ' +ISNULL(MiddleName + ' ', '') + LastName
AS CustomerName
FROM SalesLT.Customer;
```
#### 1.7) Retrieving Primary Contact Details
```SQL
-- select the CustomerID, and use COALESCE with EmailAddress and Phone columns
SELECT CustomerID, COALESCE(EmailAddress, Phone) AS PrimaryContact
FROM SalesLT.Customer;
```
#### 1.8) Retrieving Shipping Status
```SQL
SELECT SalesOrderID, OrderDate,
	CASE
		WHEN ShipDate IS NULL THEN 'Awaiting Shipment'
			ELSE 'Shipped'
		END AS ShippingStatus
	FROM SalesLT.SalesOrderHeader;
```
## 2) Querying Tables with SELECT

#### 2.1) Retrieving Transportation Report Data
```SQL
-- select unique cities, and state province
SELECT DISTINCT City, StateProvince
FROM SalesLT.Address;
```
#### 2.2) Retrieving Transportation Report Data (2)
```SQL
-- select the top 10 percent from the Name column
SELECT TOP 30 Weight, Name
FROM SalesLT.Product
-- order by the weight in descending order
ORDER BY Weight DESC;
```
#### 2.3) Retrieving Transportation Report Data (3)
```SQL
SELECT Name, Weight
FROM SalesLT.Product
ORDER BY Weight DESC
-- offset 10 rows and get the next 100
OFFSET 10 ROWS FETCH NEXT 100 ROWS ONLY;
```
#### 2.4) Retrieving Product Data
```SQL
-- select the Name, Color, and Size columns
SELECT Name, Color, Size
FROM SalesLT.Product
-- check ProductModelID is 1
WHERE ProductModelID = 1;
```
#### 2.5) Retrieving Product Data (2)
```SQL
-- select the ProductNumber and Name columns
SELECT ProductNumber, Name
FROM SalesLT.Product
-- check that Color is one of 'Black', 'Red' or 'White'
-- check that Size is one of 'S' or 'M'
WHERE Color IN ('Black', 'Red', 'White') AND Size IN ('S', 'M');
```
#### 2.6) Retrieving Product Data (3)
```SQL
-- select the ProductNumber, Name, and ListPrice columns
SELECT ProductNumber, Name, ListPrice
FROM SalesLT.Product
-- filter for product numbers beginning with BK- using LIKE
WHERE ProductNumber LIKE 'BK%';
```
#### 2.7) Retrieving Product Data (4)
```SQL
-- select the ProductNumber, Name, and ListPrice columns
SELECT ProductNumber, Name, ListPrice
FROM SalesLT.Product
-- filter for ProductNumbers
WHERE ProductNumber LIKE 'BK-[^R]%-[0-9][0-9]';
```

## 3) Querying Tables with JOIN

#### 3.1) Generating Invoice Reports
```SQL
SELECT c.CompanyName, oh.SalesOrderID, oh.TotalDue
FROM SalesLT.Customer AS c
JOIN SalesLT.SalesOrderHeader AS oh
ON c.CustomerID = oh.CustomerID;
```
#### 3.2) Generating Invoice Reports (2)
```SQL
SELECT c.CompanyName, a.AddressLine1, ISNULL(a.AddressLine2, '') AS AddressLine2, a.City, a.StateProvince, a.PostalCode, a.CountryRegion, oh.SalesOrderID, oh.TotalDue
FROM SalesLT.Customer AS c
JOIN SalesLT.SalesOrderHeader AS oh
ON oh.CustomerID = c.CustomerID
JOIN SalesLT.CustomerAddress AS ca
ON c.CustomerID = ca.CustomerID AND ca.AddressType = 'Main Office'
JOIN SalesLT.Address AS a
ON ca.AddressID = a.AddressID;
```
#### 3.3) Retrieving Sales Data
```SQL
SELECT c.CompanyName, c.FirstName, c.LastName, oh.SalesOrderID, oh.TotalDue
FROM SalesLT.Customer AS c
LEFT JOIN SalesLT.SalesOrderHeader AS oh
ON c.CustomerID = oh.CustomerID
ORDER BY oh.SalesOrderID DESC;
```
#### 3.4) Retrieving Sales Data (2)
```SQL
SELECT c.CompanyName, c.FirstName, c.LastName, c.Phone
FROM SalesLT.Customer AS c
LEFT JOIN SalesLT.CustomerAddress AS ca
ON c.CustomerID = ca.CustomerID
WHERE ca.AddressID IS NULL;
```
#### 3.5) Retrieving Sales Data (3)
```SQL
SELECT c.CustomerID, p.ProductID
FROM SalesLT.Customer AS c
FULL JOIN SalesLT.SalesOrderHeader AS oh
ON c.CustomerID = oh.CustomerID
FULL JOIN SalesLT.SalesOrderDetail AS od
ON od.SalesOrderID = oh.SalesOrderID
FULL JOIN SalesLT.Product AS p
ON p.ProductID = od.ProductID
WHERE oh. SalesOrderID IS NULL
ORDER BY ProductID, CustomerID;
```

## 4) Using SET Operators

#### 4.1) Retrieving Customer Addresses
```SQL
SELECT c.CompanyName, a.AddressLine1, a.City, 'Billing' AS AddressType
FROM SalesLT.Customer AS c
JOIN SalesLT.CustomerAddress AS ca
ON c.CustomerID = ca.CustomerID
JOIN SalesLT.Address AS a
ON a.AddressID = ca.AddressID
WHERE ca.AddressType = 'Main Office';
```
#### 4.2) Retrieving Customer Addresses (2)
```SQL
SELECT c.CompanyName, a.AddressLine1, a.City, 'Shipping' AS AddressType
FROM SalesLT.Customer AS c
JOIN SalesLT.CustomerAddress AS ca
ON c.CustomerID = ca.CustomerID
JOIN SalesLT.Address AS a
ON ca.AddressID = a.AddressID
WHERE ca.AddressType = 'Shipping';
```
#### 4.3) Retrieving Customer Addresses (3)
```SQL
SELECT c.CompanyName, a.AddressLine1, a.City, 'Billing' AS AddressType
FROM SalesLT.Customer AS c
JOIN SalesLT.CustomerAddress AS ca
ON c.CustomerID = ca.CustomerID
JOIN SalesLT.Address AS a
ON ca.AddressID = a.AddressID
WHERE ca.AddressType = 'Main Office'
UNION ALL
SELECT c.CompanyName, a.AddressLine1, a.City, 'Shipping' AS AddressType
FROM SalesLT.Customer AS c
JOIN SalesLT.CustomerAddress AS ca
ON c.CustomerID = ca.CustomerID
JOIN SalesLT.Address AS a
ON ca.AddressID = a.AddressID
WHERE ca.AddressType = 'Shipping'
```
#### 4.4) Filtering Customer Addresses (1)
```SQL
SELECT c.CompanyName
FROM SalesLT.Customer AS c
JOIN SalesLT.CustomerAddress AS ca
ON c.CustomerID = ca.CustomerID
JOIN SalesLT.Address AS a
ON ca.AddressID = a.AddressID
WHERE ca.AddressType = 'Main Office'
EXCEPT
SELECT c.CompanyName
FROM SalesLT.Customer AS c
JOIN SalesLT.CustomerAddress AS ca
ON c.CustomerID = ca.CustomerID
JOIN SalesLT.Address AS a
ON ca.AddressID = a.AddressID
WHERE ca.AddressType = 'Shipping'
ORDER BY c.CompanyName;
```
#### 4.5) Filtering Customer Addresses (2)
```SQL
select c.CompanyName
from SalesLT.Customer AS c
join SalesLT.CustomerAddress AS ca
ON c.CustomerID = ca.CustomerID
JOIN SalesLT.Address AS a
ON ca.AddressID = a.AddressID
where ca.AddressType = 'Main Office'
INTERSECT
select c.CompanyName
FROM SalesLT.Customer AS c
join SalesLT.CustomerAddress AS ca
ON c.CustomerID = ca.CustomerID
JOIN SalesLT.Address AS a
ON ca.AddressID = a.AddressID
where ca.AddressType = 'Shipping'
ORDER BY c.CompanyName;
```

## 5) Using Functions and Aggregation Data

#### 5.1) Retrieving Product Information
```SQL
SELECT ProductID, UPPER(Name) AS ProductName, ROUND(Weight, 0) AS ApproxWeight
FROM SalesLT.Product;
```
#### 5.2) Retrieving Product Information (2)
```SQL
SELECT ProductID, UPPER(Name) AS ProductName, ROUND(Weight, 0) AS ApproxWeight,
	YEAR(SellStartDate) as SellStartYear, DATENAME(m, sELLsTARTdATE) as SellStartMonth
FROM SalesLT.Product;
```
#### 5.3) Retrieving Product Information (3)
```SQL
SELECT ProductID, UPPER(Name) AS ProductName, ROUND(Weight, 0) AS ApproxWeight,
	YEAR(SellStartDate) as SellStartYear, DATENAME(m, SellStartDate) as SellStartMonth,
        LEFT(ProductNumber, 2) AS ProductType
FROM SalesLT.Product;
```
#### 5.4) Retrieving Product Information (4)
```SQL
SELECT ProductID, UPPER(Name) AS ProductName, ROUND(Weight, 0) AS ApproxWeight,
        YEAR(SellStartDate) as SellStartYear, DATENAME(m, SellStartDate) as SellStartMonth,
        LEFT(ProductNumber, 2) AS ProductType
FROM SalesLT.Product
WHERE ISNUMERIC(Size) = 1;
```
#### 5.5) Ranking Customers By Revenue
```SQL
SELECT C.CompanyName, SOH.TotalDue AS Revenue,
        RANK() OVER (ORDER BY SOH.TotalDue DESC) AS RankByRevenue
FROM SalesLT.SalesOrderHeader AS SOH
join SalesLT.Customer AS C
ON SOH.CustomerID = C.CustomerID;
```
#### 5.6) Aggregating Product Sales
```SQL
SELECT P.Name, SUM(SOD.LineTotal) AS TotalRevenue
FROM SalesLT.SalesOrderDetail AS SOD
join SalesLT.Product AS P ON SOD.ProductID = P.ProductID
GROUP BY P.Name
ORDER BY TotalRevenue DESC;
```
#### 5.7) Aggregating Product Sales (2)
```SQL
SELECT Name, SUM(LineTotal) AS TotalRevenue
FROM SalesLT.SalesOrderDetail AS SOD
JOIN SalesLT.Product AS P ON SOD.ProductID = P.ProductID
WHERE P.ListPrice > '1000'
GROUP BY P.Name
ORDER BY TotalRevenue DESC;
```
#### 5.8) Aggregating Product Sales (3)
```SQL
SELECT Name, SUM(LineTotal) AS TotalRevenue
FROM SalesLT.SalesOrderDetail AS SOD
JOIN SalesLT.Product AS P ON SOD.ProductID = P.ProductID
WHERE P.ListPrice > 1000
GROUP BY P.Name
HAVING SUM(LineTotal) > 20000
ORDER BY TotalRevenue DESC;
```

## 6) Using Subqueries and Apply

#### 6.1) Retrieving Product Price Information
```SQL
SELECT ProductID, ListPrice, Name
FROM SalesLT.Product
WHERE ListPrice >
(SELECT AVG(UnitPrice) FROM SalesLT.SalesOrderDetail)
ORDER BY ProductID;
```
#### 6.2) Retrieving Product Price Information (2)
```SQL
SELECT ProductID, Name, ListPrice
FROM SalesLT.Product
WHERE ProductID IN
	(SELECT ProductID FROM SalesLT.SalesOrderDetail
	WHERE UnitPrice < 100)
AND ListPrice >= 100
ORDER BY ProductID;
```
#### 6.3) Retrieving Product Price Information (3)
```SQL
SELECT ProductID, Name, StandardCost, ListPrice,
(SELECT avg(SOD.UnitPrice)
	FROM SalesLT.SalesOrderDetail AS SOD
	WHERE P.ProductID = SOD.ProductID) AS AvgSellingPrice
FROM SalesLT.Product AS P
ORDER BY P.ProductID;
```
#### 6.4) Retrieving Product Price Information (4)
```SQL
SELECT ProductID, Name, StandardCost, ListPrice,
(SELECT AVG(UnitPrice)
	FROM SalesLT.SalesOrderDetail AS SOD
	WHERE P.ProductID = SOD.ProductID) AS AvgSellingPrice
FROM SalesLT.Product AS P
WHERE P.StandardCost >
	(SELECT avg(UnitPrice)
	FROM SalesLT.SalesOrderDetail AS SOD
	WHERE P.ProductID = SOD.ProductID)
ORDER BY P.ProductID;
```
#### 6.5) Retrieving Customer Information
```SQL
SELECT SOH.SalesOrderID, SOH.CustomerID, CI.FirstName, CI.LastName, SOH.TotalDue
FROM SalesLT.SalesOrderHeader AS SOH
cross APPLY dbo.ufnGetCustomerInformation(SOH.CustomerID) AS CI
ORDER by SOH.SalesOrderID;
```
#### 6.6) Retrieving Customer Information (2)
```SQL
SELECT CA.CustomerID, CI.FirstName, CI.LastName, A.AddressLine1, A.City
FROM SalesLT.Address AS A
JOIN SalesLT.CustomerAddress AS CA
ON A.AddressID = CA.AddressID
cross apply dbo.ufnGetCustomerInformation(CA.CustomerID) AS CI
ORDER BY CA.CustomerID;
```

## 7) Using Table Expressions

#### 7.1) Retrieving Product Information
```SQL
SELECT P.ProductID, P.Name AS ProductName, PM.Name AS ProductModel, PM.Summary
FROM SalesLT.Product AS P
JOIN SalesLT.vProductModelCatalogDescription AS PM
ON P.ProductModelID = PM.ProductModelID
ORDER BY ProductID;
```
#### 7.2) Retrieving Product Information (2)
```SQL
DECLARE @Colors AS table (Color nvarchar(15));

INSERT INTO @Colors
SELECT DISTINCT Color FROM SalesLT.Product;

SELECT ProductID, Name, Color
FROM SalesLT.Product
WHERE Color IN (SELECT * FROM @Colors);
```
#### 7.3) Retrieving Product Information (3)
```SQL
SELECT C.ParentProductCategoryName AS ParentCategory,
        C.ProductCategoryName AS Category,
        P.ProductID, P.Name AS ProductName
FROM SalesLT.Product AS P
JOIN dbo.ufnGetAllCategories() AS C
ON P.ProductCategoryID = C.ProductCategoryID
ORDER BY ParentCategory, Category, ProductName;
```
#### 7.4) Retrieving Customer Sales Revenue
```SQL
SELECT CompanyContact, SUM(SalesAmount) AS Revenue
FROM
	(SELECT CONCAT(c.CompanyName, CONCAT(' (' + c.FirstName + ' ', c.LastName + ')')), SOH.TotalDue
	 FROM SalesLT.SalesOrderHeader AS SOH
	 JOIN SalesLT.Customer AS c
	 ON SOH.CustomerID = c.CustomerID) AS CustomerSales(CompanyContact, SalesAmount)
GROUP BY CompanyContact
ORDER BY CompanyContact;
```

## 8) Grouping Sets & Pivoting Data

#### 8.1) Retrieving Regional Sales Totals
```SQL
SELECT a.CountryRegion, a.StateProvince, SUM(soh.TotalDue) AS Revenue
FROM SalesLT.Address AS a
JOIN SalesLT.CustomerAddress AS ca
ON a.AddressID = ca.AddressID
JOIN SalesLT.Customer AS c
ON ca.CustomerID = c.CustomerID
JOIN SalesLT.SalesOrderHeader as soh
ON c.CustomerID = soh.CustomerID

-- Modify GROUP BY to use ROLLUP

GROUP BY ROLLUP (a.CountryRegion, a.StateProvince)
ORDER BY a.CountryRegion, a.StateProvince;
```
#### 8.2) Retrieving Regional Sales Totals (2)
```SQL
SELECT a.CountryRegion, a.StateProvince,
	IIF(GROUPING_ID(a.CountryRegion) = 1 AND GROUPING_ID(a.StateProvince) = 1, 'Total',
	IIF(GROUPING_ID(a.StateProvince) = 1, a.CountryRegion + ' Subtotal', a.StateProvince + ' Subtotal')) AS Level,
SUM(soh.TotalDue) AS Revenue
FROM SalesLT.Address AS a
JOIN SalesLT.CustomerAddress AS ca
ON a.AddressID = ca.AddressID
JOIN SalesLT.Customer AS c
ON ca.CustomerID = c.CustomerID
JOIN SalesLT.SalesOrderHeader as soh
ON c.CustomerID = soh.CustomerID
GROUP BY ROLLUP(a.CountryRegion, a.StateProvince)
ORDER BY a.CountryRegion, a.StateProvince;
```
#### 8.3) Retrieving Regional Sales Totals (3)
```SQL
SELECT a.CountryRegion, a.StateProvince, a.City,
CHOOSE (1 + GROUPING_ID(a.CountryRegion) + GROUPING_ID(a.StateProvince) + GROUPING_ID(a.City),
        a.City + ' Subtotal', a.StateProvince + ' Subtotal',
        a.CountryRegion + ' Subtotal', 'Total') AS Level,
SUM(soh.TotalDue) AS Revenue
FROM SalesLT.Address AS a
JOIN SalesLT.CustomerAddress AS ca
ON a.AddressID = ca.AddressID
JOIN SalesLT.Customer AS c
ON ca.CustomerID = c.CustomerID
JOIN SalesLT.SalesOrderHeader as soh
ON c.CustomerID = soh.CustomerID
GROUP BY ROLLUP(a.CountryRegion, a.StateProvince, a.City)
ORDER BY a.CountryRegion, a.StateProvince, a.City;
```
#### 8.4) Retrieving Customer Sales By Category
```SQL
SELECT * FROM
(SELECT cat.ParentProductCategoryName, cust.CompanyName, sod.LineTotal
FROM SalesLT.SalesOrderDetail AS sod
JOIN SalesLT.SalesOrderHeader AS soh ON sod.SalesOrderID = soh.SalesOrderID
JOIN SalesLT.Customer AS cust ON soh.CustomerID = cust.CustomerID
JOIN SalesLT.Product AS prod ON sod.ProductID = prod.ProductID
JOIN SalesLT.vGetAllCategories AS cat ON prod.ProductcategoryID = cat.ProductCategoryID) AS catsales
PIVOT (SUM(LineTotal) FOR ParentProductCategoryName
IN ([Accessories], [Bikes], [Clothing], [Components])) AS pivotedsales
ORDER BY CompanyName;
```

## 9) Modifying Data

#### 9.1) Inserting Products (1)
```SQL
-- Finish the INSERT statement

INSERT INTO SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, ProductCategoryID, SellStartDate)
VALUES
('LED Lights', 'LT-L123', 2.56, 12.99, 37, GETDATE());

-- Get last identity value that was inserted

SELECT SCOPE_IDENTITY();

-- Finish the SELECT statement

SELECT * FROM SalesLT.Product
WHERE ProductID = SCOPE_IDENTITY();
```

#### 9.2) Inserting Products (2)
```SQL
-- Insert a product category

INSERT INTO SalesLT.ProductCategory (ParentProductCategoryID, Name)
VALUES
(4, 'Bells and Horns');
```

#### 9.3) Inserting Products (3)
```SQL
-- Insert product category

INSERT INTO SalesLT.ProductCategory (ParentProductCategoryID, Name)
VALUES
(4, 'Bells and Horns');
```

#### 9.4) Insert 2 products
```SQL
INSERT INTO SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, ProductCategoryID, SellStartDate)
VALUES
('Bicycle Bell', 'BB-RING', 2.47, 4.99, IDENT_CURRENT('SalesLT.ProductCategory'), GETDATE()),
('Bicycle Horn', 'BB-PARP', 1.29, 3.75, IDENT_CURRENT('SalesLT.ProductCategory'), GETDATE());

-- Check if products are properly inserted

SELECT c.Name As Category, p.Name AS Product
FROM SalesLT.Product AS p
JOIN SalesLT.ProductCategory as c ON p.ProductCategoryID = c.ProductCategoryID
WHERE p.ProductCategoryID = IDENT_CURRENT('SalesLT.ProductCategory');
```

#### 9.5) Updating Products
```SQL
-- Update the SalesLT.Product table

UPDATE SalesLT.Product
SET ListPrice = ListPrice * 1.1
WHERE ProductCategoryID =
  (SELECT ProductCategoryID FROM SalesLT.ProductCategory WHERE Name = 'Bells and Horns');
```

#### 9.6) Updating Products (2)
```SQL
-- Finish the UPDATE query

UPDATE SalesLT.Product
SET DiscontinuedDate = GETDATE()
WHERE ProductCategoryID = 37 and ProductNumber <> 'LT-L123';
```

#### 9.7) Deleting Products
```SQL
-- Delete records from the SalesLT.Product table

DELETE FROM SalesLT.Product
WHERE ProductCategoryID =
	(SELECT ProductCategoryID FROM SalesLT.ProductCategory WHERE Name = 'Bells and Horns');

-- Delete records from the SalesLT.ProductCategory table

DELETE FROM SalesLT.ProductCategory
WHERE ProductCategoryID =
	(SELECT ProductCategoryID FROM SalesLT.ProductCategory WHERE Name = 'Bells and Horns');
```

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
