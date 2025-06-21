# CSIS 5300: E-commerce Database and Data Warehouse Design

This repository contains the final project for the CSIS 5300 course. The project involves the complete design, implementation, and data warehouse modeling for a new e-commerce business. It covers the full lifecycle from gathering business requirements to writing the final SQL DDL/DML and designing a star schema for analytics.

---

## 1. Business Requirements

The initial phase involved defining the core business requirements for the database.

*   **Operations to Capture:** The database needed to manage customer transactions, product inventory, customer relationships (CRM), and supplier relationships.
*   **Information to Track:** Key data points include customer details (name, address), product information (SKU, price, stock), order details (date, total amount), and supplier information.
*   **Relationships and Constraints:**
    *   A customer can place many orders (One-to-Many).
    *   An order can contain many products, and a product can be in many orders (Many-to-Many).
    *   A supplier can provide many products (One-to-Many).

---

## 2. Entity-Relationship (ER) Diagram

An ER Diagram was created using ERDPlus to model the entities and their relationships based on the business requirements.

**Entities:**
*   `Customer` (Customer_ID, Name, Address, Email)
*   `Product` (Product_ID, Name, Description, Price, Stock)
*   `Order` (Order_ID, Customer_ID, Order_Date, Total)
*   `Supplier` (Supplier_ID, Name, Contact_Info)

*(The visual ER Diagram can be found in the project files, or you can insert the image here by uploading it to the repo and adding this line: `![ER Diagram](ER-Diagram.png)`)*

---

## 3. Relational Schema & Integrity

The ER Diagram was mapped to a relational schema. Referential integrity constraints were defined to ensure data consistency.

**Relational Schema:**
*   **Customers**(`Customer_ID` (PK), Name, Address, Email)
*   **Products**(`Product_ID` (PK), Name, Description, Price, Stock, `Supplier_ID` (FK))
*   **Suppliers**(`Supplier_ID` (PK), Name, Contact_Info)
*   **Orders**(`Order_ID` (PK), `Customer_ID` (FK), Order_Date, Total)
*   **Order_Products**(`Order_ID` (FK), `Product_ID` (FK), Quantity)

**Referential Integrity Rules (On Delete/Update):**
*   **Customer Deletion:** Cascades to delete the customer's orders.
*   **Product Deletion:** Set to NO ACTION to prevent deletion if the product exists in an active order.
*   **Supplier Deletion:** Set to SET NULL on the Product table, allowing products to exist without a supplier.

**User-Defined Constraints:**
*   `Product.Stock` must be >= 0.
*   `Order.Total` must be >= 0.
*   `Order_Products.Quantity` must be > 0.
*   `Customer.Email` must follow a valid format.

---

## 4. SQL Implementation (DDL & DML)

The database was implemented using SQL. The DDL statements create the schema, and the DML statements populate it with sample data.

### DDL (CREATE TABLE Statements)

```sql
-- Supplier Table
CREATE TABLE Suppliers (
    Supplier_ID INT PRIMARY KEY,
    Name VARCHAR(255) NOT NULL,
    Contact_Info VARCHAR(255)
);

-- Product Table
CREATE TABLE Products (
    Product_ID INT PRIMARY KEY,
    Name VARCHAR(255) NOT NULL,
    Description TEXT,
    Price DECIMAL(10, 2) NOT NULL,
    Stock INT NOT NULL CHECK (Stock >= 0),
    Supplier_ID INT,
    FOREIGN KEY (Supplier_ID) REFERENCES Suppliers(Supplier_ID) ON DELETE SET NULL
);

-- Customer Table
CREATE TABLE Customers (
    Customer_ID INT PRIMARY KEY,
    Name VARCHAR(255) NOT NULL,
    Address VARCHAR(255),
    Email VARCHAR(255) UNIQUE NOT NULL CHECK (Email LIKE '%_@__%.__%')
);

-- Order Table
CREATE TABLE Orders (
    Order_ID INT PRIMARY KEY,
    Customer_ID INT,
    Order_Date DATE NOT NULL,
    Total DECIMAL(10, 2) NOT NULL CHECK (Total >= 0),
    FOREIGN KEY (Customer_ID) REFERENCES Customers(Customer_ID) ON DELETE CASCADE
);

-- Order_Product Junction Table
CREATE TABLE Order_Products (
    Order_ID INT,
    Product_ID INT,
    Quantity INT NOT NULL CHECK (Quantity > 0),
    PRIMARY KEY (Order_ID, Product_ID),
    FOREIGN KEY (Order_ID) REFERENCES Orders(Order_ID) ON DELETE CASCADE,
    FOREIGN KEY (Product_ID) REFERENCES Products(Product_ID) ON DELETE NO ACTION
);
```

### DML (INSERT Statements)

```sql
-- Sample Data
INSERT INTO Suppliers (Supplier_ID, Name, Contact_Info) VALUES
(1, 'Tech Supplies Inc.', 'contact@techsupplies.com'),
(2, 'Office Gear Ltd.', 'sales@officegear.com');

INSERT INTO Products (Product_ID, Name, Description, Price, Stock, Supplier_ID) VALUES
(101, 'Wireless Mouse', 'Ergonomic wireless mouse', 25.99, 150, 1),
(102, 'Mechanical Keyboard', 'RGB Mechanical Keyboard', 79.99, 80, 1),
(201, 'Leather Notebook', 'A5 lined leather notebook', 15.50, 200, 2);

INSERT INTO Customers (Customer_ID, Name, Address, Email) VALUES
(1, 'John Doe', '123 Maple St, Anytown', 'john.doe@example.com'),
(2, 'Jane Smith', '456 Oak Ave, Otherville', 'jane.smith@example.com');

INSERT INTO Orders (Order_ID, Customer_ID, Order_Date, Total) VALUES
(1001, 1, '2023-10-26', 105.98),
(1002, 2, '2023-10-27', 15.50);

INSERT INTO Order_Products (Order_ID, Product_ID, Quantity) VALUES
(1001, 101, 1),
(1001, 102, 1),
(1002, 201, 1);
```

---

## 5. Data Warehouse Design for Sales Analysis

To support business intelligence, a data warehouse was designed using a star schema focused on **Sales Performance**.

*   **Subject of Analysis:** Sales performance, customer buying habits, and product popularity.
*   **Granularity:** One record per sales transaction line item.
*   **Fact Table:** `Fact_Sales` containing measures like `Quantity_Sold` and `Total_Amount`.
*   **Dimension Tables:**
    *   `Dim_Customer`: Customer attributes (Name, Location).
    *   `Dim_Product`: Product attributes (Name, Category).
    *   `Dim_Date`: Time-based attributes (Day, Month, Year).

*(The visual Star Schema can be found in the project files, or you can insert it here: `![Star Schema](Star-Schema.png)`)*

---

## 6. Technologies Used

*   **ERDPlus:** For ER and Star Schema modeling.
*   **SQL:** For database definition (DDL) and manipulation (DML).
*   **Microsoft Word/PowerPoint:** For documentation and presentation.
