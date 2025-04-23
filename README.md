# Online Bookstore SQL Project

## Project Overview

**Project Title**: Online Bookstore Sales Analysis

**Database**: OnlineBookstore

This project simulates a simplified bookstore environment to showcase SQL capabilities in database creation, data loading, and analysis. It covers database and table setup, data importing from CSV files, and solving real-world business questions using SQL. It is ideal for beginners looking to gain hands-on experience with SQL and relational databases.

## Objectives
**Database Setup**: Create and define a structured relational database for the bookstore.

**Data Import**: Load customer, book, and order data from CSV files into tables.

**Exploratory Analysis**: Analyze key metrics like sales trends, customer activity, stock levels, etc.

**Advanced Insights**: Answer deeper business questions using SQL joins, aggregations, and filters.

## Project Structure

### 1. Database Setup
- **Database Creation**: A database named OnlineBookstore is created.

- **Table Creation**: Three tables were created: Books, Customers, and Orders.

```sql
CREATE DATABASE OnlineBookstore;

CREATE TABLE Books (
    Book_ID SERIAL PRIMARY KEY,
    Title VARCHAR(100),
    Author VARCHAR(100),
    Genre VARCHAR(50),
    Published_Year INT,
    Price NUMERIC(10, 2),
    Stock INT
);

CREATE TABLE Customers (
    Customer_ID SERIAL PRIMARY KEY,
    Name VARCHAR(100),
    Email VARCHAR(100),
    Phone VARCHAR(15),
    City VARCHAR(50),
    Country VARCHAR(150)
);

CREATE TABLE Orders (
    Order_ID SERIAL PRIMARY KEY,
    Customer_ID INT REFERENCES Customers(Customer_ID),
    Book_ID INT REFERENCES Books(Book_ID),
    Order_Date DATE,
    Quantity INT,
    Total_Amount NUMERIC(10, 2)
);
```

### 2. Data Import & Exploration 
- **CSV Import**: Data for all three tables was imported using the COPY command.
```sql
COPY Books(Book_ID, Title, Author, Genre, Published_Year, Price, Stock) 
FROM 'D:\SQL_30DAYS\Books.csv' CSV HEADER;

COPY Customers(Customer_ID, Name, Email, Phone, City, Country) 
FROM 'D:\SQL_30DAYS\Customers.csv' CSV HEADER;

COPY Orders(Order_ID, Customer_ID, Book_ID, Order_Date, Quantity, Total_Amount) 
FROM 'D:\SQL_30DAYS\Orders.csv' CSV HEADER;
```

- **Initial Checks**:
```sql
SELECT * FROM Books;
SELECT * FROM Customers;
SELECT * FROM Orders;
```

### 3. Data Analysis & Findings
Key business questions and queries answered:

**Retrieve all books in the "Fiction" genre**:
```sql
SELECT * FROM Books WHERE Genre='Fiction';
```

**Find books published after the year 1950**:
```sql
SELECT * FROM Books WHERE Published_Year > 1950;
```

**List all customers from the Canada**:
```sql
SELECT * FROM Customers WHERE Country='Canada';
```

**Show orders placed in November 2023**:
```sql
SELECT * FROM Orders WHERE Order_Date BETWEEN '2023-11-01' AND '2023-11-30';
```

**Retrieve the total stock of books available**:
```sql
SELECT SUM(Stock) AS Total_Stock FROM Books;
```

**Find the details of the most expensive book**:
```sql
SELECT * FROM Books ORDER BY Price DESC LIMIT 1;
```

**Show all customers who ordered more than 1 quantity of a book**:
```sql
SELECT * FROM Orders WHERE Quantity > 1;
```

**Retrieve all orders where the total amount exceeds $20**:
```sql
SELECT * FROM Orders WHERE Total_Amount > 20;
```

**List all genres available in the Books table**:
```sql
SELECT DISTINCT Genre FROM Books;
```

**Find the book with the lowest stock**:
```sql
SELECT * FROM Books ORDER BY Stock LIMIT 1;
```

**Calculate the total revenue generated from all orders**:
```sql
SELECT SUM(Total_Amount) AS Revenue FROM Orders;
```

### 4. Advanced Business Insights

**Retrieve the total number of books sold for each genre**:
```sql
SELECT b.Genre, SUM(o.Quantity) AS Total_Books_Sold
FROM Orders o
JOIN Books b ON o.Book_ID = b.Book_ID
GROUP BY b.Genre;
```

**Find the average price of books in the "Fantasy" genre**:
```sql
SELECT AVG(Price) AS Average_Price FROM Books WHERE Genre = 'Fantasy';
```

**List customers who have placed at least 2 orders**:
```sql
SELECT o.Customer_ID, c.Name, COUNT(*) AS Order_Count
FROM Orders o
JOIN Customers c ON o.Customer_ID = c.Customer_ID
GROUP BY o.Customer_ID, c.Name
HAVING COUNT(*) >= 2;
```

**Find the most frequently ordered book**:
```sql
SELECT o.Book_ID, b.Title, COUNT(*) AS Order_Count
FROM Orders o
JOIN Books b ON o.Book_ID = b.Book_ID
GROUP BY o.Book_ID, b.Title
ORDER BY Order_Count DESC LIMIT 1;
```

**Show the top 3 most expensive books of 'Fantasy' Genre**:
```sql
SELECT * FROM Books WHERE Genre='Fantasy' ORDER BY Price DESC LIMIT 3;
```

**Retrieve the total quantity of books sold by each author**:
```sql
SELECT b.Author, SUM(o.Quantity) AS Total_Books_Sold
FROM Orders o
JOIN Books b ON o.Book_ID = b.Book_ID
GROUP BY b.Author;
```

**List the cities where customers who spent over $30 are located**:
```sql
SELECT DISTINCT c.City, o.Total_Amount
FROM Orders o
JOIN Customers c ON o.Customer_ID = c.Customer_ID
WHERE o.Total_Amount > 30;
```

**Find the customer who spent the most on orders**:
```sql
SELECT c.Customer_ID, c.Name, SUM(o.Total_Amount) AS Total_Spent
FROM Orders o
JOIN Customers c ON o.Customer_ID = c.Customer_ID
GROUP BY c.Customer_ID, c.Name
ORDER BY Total_Spent DESC LIMIT 1;
```

**Calculate the stock remaining after fulfilling all orders**:
```sql
SELECT b.Book_ID, b.Title, b.Stock, COALESCE(SUM(o.Quantity), 0) AS Order_Quantity,  
       b.Stock - COALESCE(SUM(o.Quantity), 0) AS Remaining_Quantity
FROM Books b
LEFT JOIN Orders o ON b.Book_ID = o.Book_ID
GROUP BY b.Book_ID ORDER BY b.Book_ID;
```

### 5. Additional Queries for Data Insights
**List the cities with the highest number of orders placed**:
```sql
SELECT c.City, COUNT(o.Order_ID) AS Total_Orders
FROM Orders o
JOIN Customers c ON o.Customer_ID = c.Customer_ID
GROUP BY c.City
ORDER BY Total_Orders DESC;
```

**Find the author whose books have sold the most copies**:
```sql
SELECT b.Author, SUM(o.Quantity) AS Total_Books_Sold
FROM Orders o
JOIN Books b ON o.Book_ID = b.Book_ID
GROUP BY b.Author
ORDER BY Total_Books_Sold DESC
LIMIT 1;
```

**List customers who have purchased books from the highest number of different genres**:
```sql
SELECT o.Customer_ID, c.Name, COUNT(DISTINCT b.Genre) AS Unique_Genres_Bought
FROM Orders o
JOIN Books b ON o.Book_ID = b.Book_ID
JOIN Customers c ON o.Customer_ID = c.Customer_ID
GROUP BY o.Customer_ID, c.Name
ORDER BY Unique_Genres_Bought DESC
LIMIT 1;
```

**Find the month with the highest total sales**:
```sql
SELECT EXTRACT(MONTH FROM Order_Date) AS Month, SUM(Total_Amount) AS Total_Sales
FROM Orders
GROUP BY Month
ORDER BY Total_Sales DESC;
```

### Findings
- **Book Genres**: Fiction and Fantasy appear among the most queried genres.

- **Top Spend**: A small number of customers contributed significantly to total revenue.

- **Revenue Trends**: Clear monthly trends help identify seasonal peaks.

- **Stock Insights**: Remaining stock and high-demand authors were successfully identified.

### Reports
- **Sales Overview**: Total sales, revenue by genre, and quantity sold.

- **Customer Behavior**: Repeat customers, top spenders, genre preferences.

- **Stock Reports**: Inventory after fulfilling orders and lowest-stock books.

### Conclusion
This project demonstrates foundational SQL concepts including table design, importing data, joins, aggregations, and analytical queries. It provides hands-on experience useful for aspiring data analysts to solve practical business questions using SQL.

