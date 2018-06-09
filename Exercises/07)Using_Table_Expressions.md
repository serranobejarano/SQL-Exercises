## 7) Using Table Expressions

#### 7.1) Retrieving Product Information
```SQL
-- select the appropriate columns from the appropriate tables
SELECT P.ProductID, P.Name AS ProductName, PM.Name AS ProductModel, PM.Summary
FROM SalesLT.Product AS P
JOIN SalesLT.vProductModelCatalogDescription AS PM
-- join based on ProductModelID
ON P.ProductModelID = PM.ProductModelID
ORDER BY ProductID;
```

#### 7.2) Retrieving Product Information (2)
```SQL
DECLARE @Colors AS TABLE (Color nvarchar(15));

INSERT INTO @Colors
SELECT DISTINCT Color FROM SalesLT.Product;

SELECT ProductID, Name, Color
FROM SalesLT.Product
WHERE Color IN (SELECT Color FROM @Colors);
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
