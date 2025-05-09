Bookstore Database README

Overview

The bookstore database is a relational database designed to manage a bookstore's operations, including books, authors, customers, orders, and related entities. This README provides instructions for setting up the database, an overview of its structure, and guidance for usage and maintenance.
Prerequisites

A relational database management system (RDBMS) such as MySQL, MariaDB, or PostgreSQL.
Basic SQL knowledge to execute the provided commands.
Administrative access to create and manage databases.

Setup Instructions

Create the Database:
Run CREATE DATABASE bookstore; to create the database.


Select the Database:
Use USE bookstore; to switch to the bookstore database.


Create Tables:
Execute the SQL commands in the provided order to create tables, ensuring foreign key dependencies are met. The corrected SQL script is available below or in the project repository.


Populate Data:
Insert initial data into tables like country, book_language, and publisher before adding dependent data (e.g., books, author).



Database Structure
The database includes 15 tables to manage bookstore operations. Key tables and their purposes are:

country: Stores country information, used by author, publisher, and address.
book_language: Stores languages for books.
publisher: Stores publisher details.
books: Manages book inventory, including title, price, and stock quantity.
author: Stores author information.
book_author: Junction table for the many-to-many relationship between books and authors.
customer: Stores customer details, including email and phone.
customer_address: Manages additional customer addresses.
address: Stores general address information.
address_status: Stores address status options (e.g., active, inactive; currently unused).
shipping_method: Lists shipping methods and costs.
order_status: Defines order statuses (e.g., pending, shipped).
cust_order: Tracks customer orders, including total and status.
order_line: Details individual items in an order.
order_history: Records changes in order status over time.

Key Features

Foreign Key Constraints: Ensure data integrity (e.g., books references publisher and book_language).
Auto-Incrementing IDs: Primary keys use AUTO_INCREMENT for unique identification.
Normalized Design: Tables are structured to minimize redundancy and support efficient queries.

SQL Script
The corrected SQL script to create the database and tables is provided below. Ensure tables are created in the listed order to avoid foreign key errors.
-- Create Database
CREATE DATABASE bookstore;

-- Use the Database
USE bookstore;

-- Create Tables
CREATE TABLE country (
    country_id INT AUTO_INCREMENT PRIMARY KEY,
    country_name VARCHAR(100) NOT NULL
);

CREATE TABLE book_language (
    language_id INT AUTO_INCREMENT PRIMARY KEY,
    language_name VARCHAR(100) NOT NULL
);

CREATE TABLE publisher (
    publisher_id INT AUTO_INCREMENT PRIMARY KEY,
    publisher_name VARCHAR(255) NOT NULL,
    country_id INT,
    FOREIGN KEY (country_id) REFERENCES country(country_id)
);

CREATE TABLE books (
    book_id INT AUTO_INCREMENT PRIMARY KEY,
    title VARCHAR(255) NOT NULL,
    publisher_id INT,
    language_id INT,
    publication_year INT,
    genre VARCHAR(100),
    price DECIMAL(10, 2),
    stock_quantity INT,
    FOREIGN KEY (publisher_id) REFERENCES publisher(publisher_id),
    FOREIGN KEY (language_id) REFERENCES book_language(language_id)
);

CREATE TABLE author (
    author_id INT AUTO_INCREMENT PRIMARY KEY,
    first_name VARCHAR(100) NOT NULL,
    last_name VARCHAR(100) NOT NULL,
    country_id INT,
    biography TEXT,
    FOREIGN KEY (country_id) REFERENCES country(country_id)
);

CREATE TABLE book_author (
    book_id INT NOT NULL,
    author_id INT NOT NULL,
    PRIMARY KEY (book_id, author_id),
    FOREIGN KEY (book_id) REFERENCES books(book_id),
    FOREIGN KEY (author_id) REFERENCES author(author_id)
);

CREATE TABLE customer (
    customer_id INT AUTO_INCREMENT PRIMARY KEY,
    full_name VARCHAR(255) NOT NULL,
    email VARCHAR(255) NOT NULL UNIQUE,
    phone VARCHAR(20),
    address TEXT
);

CREATE TABLE customer_address (
    address_id INT AUTO_INCREMENT PRIMARY KEY,
    customer_id INT NOT NULL,
    address_line1 VARCHAR(255) NOT NULL,
    city VARCHAR(100) NOT NULL,
    postal_code VARCHAR(20) NOT NULL,
    country VARCHAR(100) NOT NULL,
    FOREIGN KEY (customer_id) REFERENCES customer(customer_id)
);

CREATE TABLE address (
    address_id INT AUTO_INCREMENT PRIMARY KEY,
    address_line1 VARCHAR(255) NOT NULL,
    city VARCHAR(100) NOT NULL,
    postal_code VARCHAR(20) NOT NULL,
    country VARCHAR(100) NOT NULL,
    country_id INT,
    FOREIGN KEY (country_id) REFERENCES country(country_id)
);

CREATE TABLE address_status (
    status_id INT AUTO_INCREMENT PRIMARY KEY,
    status_name VARCHAR(100) NOT NULL
);

CREATE TABLE shipping_method (
    shipping_method_id INT AUTO_INCREMENT PRIMARY KEY,
    method_name VARCHAR(100) NOT NULL UNIQUE,
    cost DECIMAL(10, 2) NOT NULL
);

CREATE TABLE order_status (
    status_id INT AUTO_INCREMENT PRIMARY KEY,
    status_name VARCHAR(100) NOT NULL UNIQUE
);

CREATE TABLE cust_order (
    order_id INT AUTO_INCREMENT PRIMARY KEY,
    customer_id INT,
    order_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    shipping_method_id INT,
    order_status_id INT,
    total DECIMAL(10, 2),
    FOREIGN KEY (customer_id) REFERENCES customer(customer_id),
    FOREIGN KEY (shipping_method_id) REFERENCES shipping_method(shipping_method_id),
    FOREIGN KEY (order_status_id) REFERENCES order_status(order_status_id)
);

CREATE TABLE order_line (
    order_line_id INT AUTO_INCREMENT PRIMARY KEY,
    order_id INT NOT NULL,
    book_id INT NOT NULL,
    quantity INT NOT NULL,
    price DECIMAL(10, 2) NOT NULL,
    FOREIGN KEY (order_id) REFERENCES cust_order(order_id),
    FOREIGN KEY (book_id) REFERENCES books(book_id)
);

CREATE TABLE order_history (
    history_id INT AUTO_INCREMENT PRIMARY KEY,
    order_id INT NOT NULL,
    status_id INT NOT NULL,
    change_date DATETIME DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (order_id) REFERENCES cust_order(order_id),
    FOREIGN KEY (status_id) REFERENCES order_status(status_id)
);

Usage

Inserting Data:
Populate tables in order of dependency (e.g., country before publisher, books before book_author).
Example: Insert into country (INSERT INTO country (country_name) VALUES ('USA');) before adding publishers.


Querying Data:
Use JOINs to retrieve related data (e.g., books with authors: SELECT b.title, a.first_name, a.last_name FROM books b JOIN book_author ba ON b.book_id = ba.book_id JOIN author a ON ba.author_id = a.author_id;).


Maintaining Data:
Update books.stock_quantity when orders are placed.
Log order status changes in order_history.



Notes

Foreign Key Dependencies: Create tables in the provided order to avoid errors.
Unused Table: The address_status table is not currently referenced; evaluate its necessity.
Data Types: Ensure inserted data matches column types (e.g., DECIMAL(10,2) for prices, TIMESTAMP for dates).
RDBMS Compatibility: The script uses MySQL/MariaDB syntax (AUTO_INCREMENT, FOREIGN KEY). Adjust for other RDBMS if needed (e.g., PostgreSQL uses SERIAL).

Troubleshooting

Foreign Key Errors: Verify that referenced tables and rows exist before creating dependent tables or inserting data.
Table Creation Order: Follow the script sequence to satisfy dependencies.
Data Integrity: Ensure unique constraints (e.g., customer.email, shipping_method.method_name) are respected.

Future Enhancements

Add indexes on frequently queried columns (e.g., books.title, customer.email) for performance.
Implement triggers to update stock_quantity automatically when orders are placed.
Create views for common queries (e.g., books with authors and publishers).

Contact
For support or questions, contact the bookstore's IT team or database administrator.
