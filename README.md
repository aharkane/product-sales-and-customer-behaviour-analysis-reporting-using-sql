# Adventureworks Product Sales and Customer Behaviour Analysis
## Table of Contents

1. [Executive Summary](#executive-summary)
2. [Project Architecture](#project-architecture)
3. [Featured SQL Examples](#featured-sql-examples)
   1. [Product Reporting with Conditional Logic](#product-reporting-with-conditional-logic)
   2. [Customer Profiles with Aggregations](#customer-profiles-with-aggregations)
   3. [Hierarchical Category Analysis](#hierarchical-category-analysis)
   4. [Sales Performance Analysis with HAVING](#sales-performance-analysis-with-having)
   5. [Insert Operations with Identity Management](#insert-operations-with-identity-management)
   6. [Multi-Row Insert with IDENT_CURRENT](#multi-row-insert-with-ident_current)
   7. [Update Operations with Conditional Criteria](#update-operations-with-conditional-criteria)
   8. [Delete with Referential Integrity](#delete-with-referential-integrity)


## Executive Summary

**Overview**

T-SQL project covering business intelligence reporting and database operations.Covers product analysis, customer profiling, category hierarchies, sales metrics, and Create, Read, Update and Delete (CRUD) operations.

**Tasks and Goals**

- Build business intelligence reports for stakeholders across sales, marketing, and operations
- Demonstrate advanced SQL query design and optimization techniques
- Implement data maintenance workflows including inserts, updates, and deletes
- Handle data quality issues with missing values and complex joins
- Create actionable insights from multi-dimensional sales and product data

**Key Accomplishments** 

✓ Designed and executed 5 major business reporting workflows  
✓ Implemented advanced SQL techniques (hierarchical queries, aggregations, window functions)  
✓ Handled data quality issues with missing values and NULL management  
✓ Performed complete CRUD operations (Create, Read, Update, Delete) with transaction control  
✓ Built queries supporting multi-stakeholder reporting needs (sales, marketing, operations)  
✓ Demonstrated data maintenance best practices with identity management and referential integrity  

---
## Project Architecture

**Technology Stack**

| Component | Technology |
|-----------|-----------|
| **Database** | SQL Server (AdventureWorks Sample Database) |
| **Query Language** | T-SQL |
| **Environment** | Jupyter Notebook with SQL Kernel |
| **Data Analysis** | Python/Pandas for result processing |
| **Connection** | ODBC Driver 17/18 for SQL Server |

**Repository Structure**

```
├── adventureworks-sql-analytics.ipynb
└── README.md
```

## Featured SQL Examples

### Product Reporting with Conditional Logic

```sql
-- Comprehensive product analysis with calculated fields and categorization
SELECT TOP 100
    ProductID,
    Name AS ProductName,
    ProductNumber,
    StandardCost,
    ListPrice,
    ListPrice - StandardCost AS Markup,
    COALESCE(NULLIF(Color, ''), 'No Color') 
        + ' ' + COALESCE(NULLIF(Size, ''), 'No Size') AS FullProductDescription,
    CASE 
        WHEN ListPrice < 50 THEN 'Low'
        WHEN ListPrice BETWEEN 50 AND 500 THEN 'Medium'
        ELSE 'High'
    END AS PriceCategory
FROM SalesLT.Product
ORDER BY ListPrice DESC;
```

### Customer Profiles with Aggregations

```sql
-- Customer overview with order history and revenue calculations
SELECT TOP 100
    c.CustomerID,
    COALESCE(c.Title, '') + ' ' + c.FirstName 
        + COALESCE(' ' + c.MiddleName, '') + ' ' + c.LastName AS FullCustomerName,
    c.CompanyName,
    COALESCE(NULLIF(c.EmailAddress, ''), NULLIF(c.Phone, ''), 'No Contact Info') AS PrimaryContact,
    COUNT(oh.SalesOrderID) AS NumberOfOrders,
    COALESCE(SUM(oh.SubTotal + oh.TaxAmt + oh.Freight), 0) AS TotalRevenue
FROM SalesLT.Customer AS c
LEFT JOIN SalesLT.SalesOrderHeader AS oh ON c.CustomerID = oh.CustomerID
GROUP BY 
    c.CustomerID, c.Title, c.FirstName, c.MiddleName, c.LastName, 
    c.CompanyName, c.EmailAddress, c.Phone
ORDER BY NumberOfOrders DESC;
```

### Hierarchical Category Analysis

```sql
-- Product category hierarchy showing parent-child relationships
SELECT TOP 100
    COALESCE(pcat.Name, 'Uncategorized') AS ParentCategory,
    COALESCE(cat.Name, 'No Subcategory') AS SubCategory,
    COUNT(p.ProductID) AS TotalProducts
FROM SalesLT.ProductCategory AS cat
LEFT JOIN SalesLT.ProductCategory AS pcat 
    ON cat.ParentProductCategoryID = pcat.ProductCategoryID
LEFT JOIN SalesLT.Product AS p 
    ON cat.ProductCategoryID = p.ProductCategoryID
GROUP BY pcat.Name, cat.Name
ORDER BY ParentCategory, SubCategory;
```

### Sales Performance Analysis with HAVING

```sql
-- Product sales metrics with filtering and performance ranking
SELECT
    p.ProductID,
    p.Name AS ProductName,
    p.ListPrice,
    SUM(od.OrderQty) AS TotalUnitsSold,
    AVG(od.UnitPrice) AS AverageSellingPrice
FROM SalesLT.Product AS p
JOIN SalesLT.SalesOrderDetail AS od ON p.ProductID = od.ProductID
WHERE p.ListPrice > 1000
GROUP BY p.ProductID, p.Name, p.ListPrice
HAVING SUM(od.OrderQty) > 20
ORDER BY TotalUnitsSold DESC;
```

### Insert Operations with Identity Management

```sql
-- Insert new product with identity generation and verification
INSERT INTO SalesLT.Product 
    (Name, ProductNumber, StandardCost, ListPrice, ProductCategoryID, SellStartDate)
VALUES 
    ('LED Lights', 'LT-L123', 2.56, 12.99, 37, GETDATE())
COMMIT;

SELECT SCOPE_IDENTITY() AS NewProductID;
```

### Multi-Row Insert with IDENT_CURRENT

```sql
-- Insert new category with dependent products using current identity
INSERT INTO SalesLT.ProductCategory (ParentProductCategoryID, Name)
VALUES (4, 'Bells and Horns');

INSERT INTO SalesLT.Product 
    (Name, ProductNumber, StandardCost, ListPrice, ProductCategoryID, SellStartDate)
VALUES 
    ('Bicycle Bell', 'BB-RING', 2.47, 4.99, IDENT_CURRENT('SalesLT.ProductCategory'), GETDATE()),
    ('Bicycle Horn', 'BH-PARP', 1.29, 3.75, IDENT_CURRENT('SalesLT.ProductCategory'), GETDATE());

COMMIT;
```

### Update Operations with Conditional Criteria

```sql
-- Price adjustment across product category
UPDATE SalesLT.Product
SET ListPrice = ListPrice * 1.1
WHERE ProductCategoryID = 
    (SELECT ProductCategoryID FROM SalesLT.ProductCategory WHERE Name = 'Bells and Horns');

COMMIT;
```

### Delete with Referential Integrity

```sql
-- Remove products and category while maintaining data integrity
DELETE FROM SalesLT.Product
WHERE ProductCategoryID = 
    (SELECT ProductCategoryID FROM SalesLT.ProductCategory WHERE Name = 'Bells and Horns');

DELETE FROM SalesLT.ProductCategory
WHERE Name = 'Bells and Horns';

COMMIT;
```
