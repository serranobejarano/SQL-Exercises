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
