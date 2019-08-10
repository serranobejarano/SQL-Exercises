## 4) Using SET Operators

#### 4.1) Retrieving Customer Addresses
```SQL
-- select the CompanyName, AddressLine1 columns
-- alias as per the instructions
SELECT c.CompanyName, a.AddressLine1, a.City, 'Billing' AS AddressType
FROM SalesLT.Customer AS c
JOIN SalesLT.CustomerAddress AS ca
-- join based on CustomerID
ON c.CustomerID = ca.CustomerID
-- join another table
JOIN SalesLT.Address AS a
-- join based on AddressID
ON a.AddressID = ca.AddressID
-- filter for where the correct AddressType
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
-- edit this as per the instructions
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
-- join the CustomerAddress table
JOIN SalesLT.CustomerAddress AS ca
ON c.CustomerID = ca.CustomerID
JOIN SalesLT.Address AS a
-- join based on AddressID
ON ca.AddressID = a.AddressID
WHERE ca.AddressType = 'Main Office'
EXCEPT
SELECT c.CompanyName
FROM SalesLT.Customer AS c
-- use the appropriate join to join the CustomerAddress table
JOIN SalesLT.CustomerAddress AS ca
-- join based on CustomerID
ON c.CustomerID = ca.CustomerID
-- use the appropriate join to join the Address table
JOIN SalesLT.Address AS a
ON ca.AddressID = a.AddressID
-- filter for the appropriate AddressType
WHERE ca.AddressType = 'Shipping'
ORDER BY c.CompanyName;
```
#### 4.5) Filtering Customer Addresses (2)
```SQL
-- select the CompanyName column
SELECT c.CompanyName
-- from the appropriate table
FROM SalesLT.Customer AS c
-- use the appropriate join with the appropriate table
JOIN SalesLT.CustomerAddress AS ca
-- join based on CustomerID
ON c.CustomerID = ca.CustomerID
-- use the appropriate join with the appropriate table
JOIN SalesLT.Address AS a
-- join based on AddressID
ON ca.AddressID = a.AddressID
-- filter based on AddressType
WHERE ca.AddressType = 'Main Office'
INTERSECT
-- select the CompanyName column
SELECT c.CompanyName
FROM SalesLT.Customer AS c
-- use the appropriate join with the appropriate table
JOIN SalesLT.CustomerAddress AS ca
ON c.CustomerID = ca.CustomerID
JOIN SalesLT.Address AS a
-- join based on AddressID
ON ca.AddressID = a.AddressID
-- filter based on AddressType
WHERE ca.AddressType = 'Shipping'
ORDER BY c.CompanyName;
```
