## 6) Using Subqueries and Apply

#### 6.1) Retrieving Product Price Information
```SQL
-- select the ProductID, Name, and ListPrice columns
SELECT ProductID, ListPrice, Name
FROM SalesLT.Product
-- filter based on ListPrice
WHERE ListPrice >
-- get the average UnitPrice
(SELECT AVG(UnitPrice) FROM SalesLT.SalesOrderDetail)
ORDER BY ProductID;
```
#### 6.2) Retrieving Product Price Information (2)
```SQL
SELECT ProductID, Name, ListPrice
FROM SalesLT.Product
WHERE ProductID IN
  -- select ProductID from the appropriate table
  (SELECT ProductID FROM SalesLT.SalesOrderDetail
  WHERE UnitPrice < 100)
AND ListPrice >= 100
ORDER BY ProductID;
```
#### 6.3) Retrieving Product Price Information (3)
```SQL
SELECT ProductID, Name, StandardCost, ListPrice,
-- get the average UnitPrice
(SELECT avg(SOD.UnitPrice)
-- from the appropriate table, aliased as SOD
  FROM SalesLT.SalesOrderDetail AS SOD
  -- filter when the appropriate ProductIDs are equal
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
-- filter based on StandardCost
WHERE P.StandardCost >
  -- get the average UnitPrice
	(SELECT avg(UnitPrice)
  -- from the appropriate table aliased as SOD
	FROM SalesLT.SalesOrderDetail AS SOD
  -- filter when the appropriate ProductIDs are equal
	WHERE P.ProductID = SOD.ProductID)
ORDER BY P.ProductID;
```
#### 6.5) Retrieving Customer Information
```SQL
-- select SalesOrderID, CustomerID, FirstName, LastName, TotalDue from the appropriate tables
SELECT SOH.SalesOrderID, SOH.CustomerID, CI.FirstName, CI.LastName, SOH.TotalDue
FROM SalesLT.SalesOrderHeader AS SOH
-- cross apply as per the instructions
CROSS APPLY dbo.ufnGetCustomerInformation(SOH.CustomerID) AS CI
-- finish the clause
ORDER BY SOH.SalesOrderID;
```
#### 6.6) Retrieving Customer Information (2)
```SQL
-- select the CustomerID, FirstName, LastName, Addressline1, and City columns from the appropriate tables
SELECT CA.CustomerID, CI.FirstName, CI.LastName, A.AddressLine1, A.City
FROM SalesLT.Address AS A
JOIN SalesLT.CustomerAddress AS CA
-- join based on AddressID
ON A.AddressID = CA.AddressID
-- cross apply as per instructions
CROSS APPLY dbo.ufnGetCustomerInformation(CA.CustomerID) AS CI
ORDER BY CA.CustomerID;
```
