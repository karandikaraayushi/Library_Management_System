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
REFERENCES branch(branch_id)
``` 

2)  **CRUD Operations**
Create: Inserted sample records into the books table.
Read: Retrieved and displayed data from various tables.
Update: Updated records in the employees table.
Delete: Removed records from the members table as needed.

Task 1. Create a New Book Record -- "978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.')"
```sql 
insert into books values ('978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.')
```

Task 2. Update an Existing Member's address
```sql
update members set member_address="125 Main St" where member_id="C101"
select * from members
```

-- Q3. Delete a Record from the Issued Status Table
```sql
delete from issued_status where issued_id="IS121"
select * from issued_status 
```

-- Q4. Retrieve All Books Issued by a Specific Employee
```sql
select * from issued_status where issued_emp_id="E101"
```

-- Q5. List members who have issued more than one book
```sql
select issued_emp_id, count(*) from issued_status GROUP BY issued_emp_id HAVING count(issued_id)>1
select * from issued_status
```

-- Q6. Create Summary Tables: Used CTAS to generate new tables based on query results - each book and total book_issued_cnt*
```sql
Create table book_issued_cnt AS
SELECT books.book_title, books.isbn, count(issued_status.issued_id) from issued_status
JOIN books ON books.isbn=issued_status.issued_book_isbn
GROUP BY books.book_title, books.isbn

select * from book_issued_cnt
```

-- Q7. Retrieve All Books in a Specific Category
```sql
select book_title from books where category="Children"
```

-- Q8. Find Total Rental Income by Category
```sql
select category, sum(rental_price) as rental_income from books GROUP BY category ORDER BY rental_income DESC 
```

-- Q9. List Members Who Registered in the Last 180 Days:
```sql
select member_name,reg_date from members where reg_date>='2024-01-01'
```

-- Q10. List Employees with Their Branch Manager's Name and their branch details:
```sql
select employees.*,employees.emp_id,branch.manager_id,branch.branch_address,branch.contact_no 
from branch join employees on branch.branch_id=employees.branch_id 
group by employees.emp_id
```
-- Q11. Create a Table of Books with Rental Price Above a Certain Threshold:
```sql
CREATE TABLE expensive_books AS 
select * from books where rental_price>7
```

-- Q12. Retrieve the List of Books Not Yet Returned
```sql
SELECT * FROM issued_status 
LEFT JOIN return_status ON issued_status.issued_id = return_status.issued_id;
```

-- Q13. Identify Members with Overdue Books
-- Write a query to identify members who have overdue books (assume a 30-day return period). 
-- Display the member's_id, member's name, book title, issue date, and days overdue.
```sql
SELECT 
  members.member_id, 
  members.member_name, 
  books.book_title, 
  issued_status.issued_date,
  (CURRENT_DATE - issued_status.issued_date) - 30 AS overdue_days
FROM members 
JOIN issued_status 
  ON members.member_id = issued_status.issued_member_id
JOIN books 
  ON books.isbn = issued_status.issued_book_isbn
LEFT JOIN return_status 
  ON issued_status.issued_id = return_status.issued_id
WHERE 
  return_status.return_date IS NULL
  AND (CURRENT_DATE - issued_status.issued_date) > 30;
```

-- Q15. Branch Performance Report. Create a query that generates a performance report for each branch, 
-- showing the number of books issued, the number of books returned, and the total revenue generated from book rentals.

```sql
CREATE TABLE branch_performance_report_ AS 
SELECT 
    branch.branch_id,
    COUNT(issued_status.issued_id) AS number_of_books_issued,
    COUNT(return_status.return_id) AS number_of_books_returned,
    SUM(books.rental_price) AS total_revenue
FROM issued_status
JOIN employees 
    ON employees.emp_id = issued_status.issued_emp_id
JOIN branch 
    ON employees.branch_id = branch.branch_id
LEFT JOIN return_status 
    ON return_status.issued_id = issued_status.issued_id
JOIN books 
    ON issued_status.issued_book_isbn = books.isbn
GROUP BY branch.branch_id;
select * from branch_performance_report_
```

-- Q16. CTAS: Create a Table of Active Members. Use the CREATE TABLE AS (CTAS) statement 
-- to create a new table active_members containing members who have issued at least one book in the last 2 months.

```sql
CREATE TABLE active_memberss AS 
SELECT issued_member_id AS active_members
FROM issued_status
WHERE issued_date BETWEEN '2024-04-10' AND '2024-05-10'
GROUP BY issued_member_id
HAVING COUNT(issued_book_name) > 0; -- made 0 cause all have issued only 1 book
SELECT * FROM active_memberss
```

-- Q17. Find Employees with the Most Book Issues Processed
-- Write a query to find the top 3 employees who have processed the most book issues. 
-- Display the employee name, number of books processed, and their branch.

```sql
SELECT 
  employees.emp_name, 
  COUNT(*) AS books_processed, 
  employees.branch_id
FROM employees 
JOIN issued_status 
  ON employees.emp_id = issued_status.issued_emp_id
JOIN books 
  ON books.isbn = issued_status.issued_book_isbn
WHERE books.status = 'yes'
GROUP BY employees.emp_name, employees.branch_id
ORDER BY books_processed DESC
LIMIT 3;
```

**Reports**
Database Schema: Detailed table structures and relationships.
Data Analysis: Insights into book categories, employee salaries, member registration trends, and issued books.
Summary Reports: Aggregated data on high-demand books and employee performance.

**Conclusion**
This project demonstrates the application of SQL skills in creating and managing a library management system. It includes database setup, data manipulation, and advanced querying, providing a solid foundation for data management and analysis.

**Followed @Zero Analyst Tutorial**






