# Library_Management_System

Project Title: Library Management System
Level: Intermediate
Database: library_project

This project demonstrates the implementation of a Library Management System using SQL. It includes creating and managing tables, performing CRUD operations, and executing intermediate SQL queries. The goal is to showcase skills in database design, manipulation, and querying.

**Objectives**
1. Set up the Library Management System Database: Create and populate the database with tables for branches, employees, members, books, issued status, and return status.
2. CRUD Operations: Perform Create, Read, Update, and Delete operations on the data.
3. CTAS (Create Table As Select): Utilize CTAS to create new tables based on query results.
4. Advanced SQL Queries: Develop complex queries to analyze and retrieve specific data.

1) **DATABASE SETUP**
<img width="1108" height="796" alt="image" src="https://github.com/user-attachments/assets/83e3d7e0-447e-425f-85a0-d04a9e786274" />

 - Database Creation: Created a database named library_db.
 - Table Creation: Created tables for branches, employees, members, books, issued status, and return status. Each table includes relevant columns and relationships.

```sql
create database library_project;
use library_project;
select * from branch ;
select * from employees;
select * from books;
create table members(member_id varchar(10) PRIMARY KEY, member_name varchar(30), member_address varchar(80), reg_date DATE)
select * from members;
create table issued_status(issued_id varchar(10) PRIMARY KEY, issued_member_id varchar(10), issued_book_name varchar(80), 
issued_date	DATE, issued_book_isbn varchar(40), issued_emp_id varchar(10))
select * from issued_status;
create table return_status (return_id varchar(10) PRIMARY KEY, issued_id varchar(10),	
return_book_name varchar(80), return_date DATE,	return_book_isbn varchar(20))
select * from return_status;
ALTER TABLE branch ADD PRIMARY KEY (branch_id(20));
SHOW KEYS FROM books WHERE Key_name = 'PRIMARY';
ALTER TABLE employees ADD PRIMARY KEY (emp_id(20));
ALTER TABLE books ADD PRIMARY KEY (isbn(50));

-- FORIEGN KEY ASSIGNING

ALTER TABLE issued_status
ADD CONSTRAINT fk_member_id
FOREIGN KEY (issued_member_id)
REFERENCES members(member_id)

ALTER TABLE books MODIFY isbn varchar(40) 

ALTER TABLE issued_status
ADD CONSTRAINT fk_books
FOREIGN KEY (issued_book_isbn)
REFERENCES books(isbn)

ALTER TABLE return_status
ADD CONSTRAINT fk_return
FOREIGN KEY (return_book_isbn)
REFERENCES books(isbn)


ALTER TABLE employees MODIFY emp_id varchar(10) 

ALTER TABLE issued_status
ADD CONSTRAINT fk_employee
FOREIGN KEY (issued_emp_id)
REFERENCES employees(emp_id)

Delete from return_status where (issued_id) = 'IS101';
Delete from return_status where (issued_id) = 'IS105';
Delete from return_status where (issued_id) = 'IS103';

ALTER TABLE return_status
ADD CONSTRAINT fk_issued_status
FOREIGN KEY (issued_id)
REFERENCES issued_status(issued_id);
#SELECT issued_id
#FROM return_status
#WHERE issued_id NOT IN (SELECT issued_id FROM issued_status);

ALTER TABLE branch MODIFY branch_id varchar(20) 
ALTER TABLE employees MODIFY branch_id varchar(20) 

ALTER TABLE employees
ADD CONSTRAINT fk_employeeid
FOREIGN KEY (branch_id)
REFERENCES branch(branch_id) ``` 

2)  **CRUD Operations**
Create: Inserted sample records into the books table.
Read: Retrieved and displayed data from various tables.
Update: Updated records in the employees table.
Delete: Removed records from the members table as needed.

Task 1. Create a New Book Record -- "978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.')"
```sql 
insert into books values ('978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.')```





