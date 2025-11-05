# AdventureWorks Business Reporting & Data Maintenance Project

## Project Overview

A comprehensive **T-SQL data analytics and maintenance project** focused on the AdventureWorks database. This project demonstrates practical SQL expertise through business-oriented reporting, data analysis, and database maintenance operations on a real-world sales and product dataset.

## Project Goals

- Build business intelligence reports for stakeholders across sales, marketing, and operations
- Demonstrate advanced SQL query design and optimization techniques
- Implement data maintenance workflows including inserts, updates, and deletes
- Handle data quality issues with missing values and complex joins
- Create actionable insights from multi-dimensional sales and product data

## Technology Stack

| Component | Technology |
|-----------|-----------|
| **Database** | SQL Server (AdventureWorks Sample Database) |
| **Query Language** | T-SQL |
| **Environment** | Jupyter Notebook with SQL Kernel |
| **Data Analysis** | Python/Pandas for result processing |
| **Connection** | ODBC Driver 17/18 for SQL Server |

## Project Scope

This project covers five major business reporting and maintenance areas:

1. **Comprehensive Product Reporting** - Detailed product inventory with pricing and profit analysis
2. **Customer and Order Insights** - Customer profiles with order history and revenue tracking
3. **Product Category Hierarchy** - Parent-child category relationships with product distribution
4. **Advanced Sales Analysis** - Sales aggregation, performance metrics, and filtering
5. **Data Maintenance Operations** - Insert, update, and delete operations with transaction management

## Featured SQL Implementations

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

**Skills Demonstrated:**
- COALESCE and NULLIF for handling missing data
- CASE statements for conditional categorization
- Calculated fields (Markup)
- String concatenation
- Multi-column ordering

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

**Skills Demonstrated:**
- LEFT JOIN for left-outer relationships
- GROUP BY with aggregation functions (COUNT, SUM)
- String concatenation with NULL handling
- Multi-criteria filtering with COALESCE

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

**Skills Demonstrated:**
- Self-joins for hierarchical relationships
- Multiple LEFT JOINs
- NULL value handling in GROUP BY
- Logical ordering of hierarchical data

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

**Skills Demonstrated:**
- JOIN operations with aggregation
- WHERE vs. HAVING filtering (pre-aggregation vs. post-aggregation)
- AVG and SUM functions
- Multi-column grouping and ordering

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

**Skills Demonstrated:**
- IDENTITY management with SCOPE_IDENTITY()
- INSERT with specific columns
- Transaction control (COMMIT)

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

**Skills Demonstrated:**
- Multiple INSERT statements with dependencies
- IDENT_CURRENT for referencing newly created identity values
- Multi-row data insertion
- Transaction management

### Update Operations with Conditional Criteria

```sql
-- Price adjustment across product category
UPDATE SalesLT.Product
SET ListPrice = ListPrice * 1.1
WHERE ProductCategoryID = 
    (SELECT ProductCategoryID FROM SalesLT.ProductCategory WHERE Name = 'Bells and Horns');

COMMIT;
```

**Skills Demonstrated:**
- UPDATE with subquery filtering
- Calculated updates with expressions
- Transaction control

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

**Skills Demonstrated:**
- DELETE operations with subquery filtering
- Referential integrity handling
- Parent-child relationship management

## Key Accomplishments

✓ Designed and executed 5 major business reporting workflows  
✓ Implemented advanced SQL techniques (hierarchical queries, aggregations, window functions)  
✓ Handled data quality issues with missing values and NULL management  
✓ Performed complete CRUD operations (Create, Read, Update, Delete) with transaction control  
✓ Built queries supporting multi-stakeholder reporting needs (sales, marketing, operations)  
✓ Demonstrated data maintenance best practices with identity management and referential integrity  

## Repository Structure

```
├── adventureworks-sql-analytics.ipynb
└── README.md
```

## Skills Demonstrated

**SQL Query Design:**
- Complex SELECT with multiple JOINs
- Aggregation functions (SUM, AVG, COUNT)
- GROUP BY and HAVING clauses
- CASE statements and conditional logic
- Subqueries and derived tables
- String manipulation and NULL handling

**Database Operations:**
- CRUD operations (INSERT, UPDATE, DELETE, SELECT)
- Transaction management (BEGIN, COMMIT)
- Identity value management (SCOPE_IDENTITY, IDENT_CURRENT)
- Data integrity and referential constraints
- Data maintenance workflows

**Business Analysis:**
- Multi-dimensional business reporting
- Performance metrics calculation
- Customer segmentation and analysis
- Product portfolio analysis
- Sales performance tracking

**Data Quality:**
- Missing value handling with COALESCE and NULLIF
- Data validation in reports
- Consistent data representation
- Hierarchical data presentation

---

*This project demonstrates T-SQL mastery applied to real-world business reporting and database maintenance scenarios.*
