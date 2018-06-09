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
