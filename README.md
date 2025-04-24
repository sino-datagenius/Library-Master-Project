# Library Management System

## Project Overview

**Project Title**: Library Management System  
**Level**: Intermediate  
**Database**: `Library Management System`

This project demonstrates the implementation of a Library Management System using SQL. It includes creating and managing tables, performing CRUD operations, and executing advanced SQL queries. The goal is to showcase skills in database design, manipulation, and querying.

![Library management](https://github.com/user-attachments/assets/384c1e50-e1f3-40f6-895a-d90c2d937592)


## Objectives

1. **Set up the Library Management System Database**: Create and populate the database with tables for branches, employees, members, books, issued status, and return status.
2. **CRUD Operations**: Perform Create, Read, Update, and Delete operations on the data.
3. **CTAS (Create Table As Select)**: Utilize CTAS to create new tables based on query results.
4. **Advanced SQL Queries**: Develop complex queries to analyze and retrieve specific data.

## Project Structure

### 1. Database Setup

- **Database Creation**: Created a database named `Library Management System`.
- **Table Creation**: Created tables for branches, employees, members, books, issued status, and return status. Each table includes relevant columns and relationships.

```sql

CREATE DATABASE Library_Management_System;

--Creating Branch Table --


DROP TABLE IF EXISTS branch;

CREATE TABLE branch (
    branch_id VARCHAR(20) PRIMARY KEY,
    manager_id VARCHAR(20),
    branch_address VARCHAR(55),
    contact_no VARCHAR(25)
);


-- Creating Employee Table --


DROP TABLE IF EXISTS employees;

CREATE TABLE employees (
    emp_id VARCHAR(25) PRIMARY KEY,
    emp_name VARCHAR(50),
    position VARCHAR(20),
    salary FLOAT,
    branch_id VARCHAR(25) --FK
);



--Creating Books Table --



DROP TABLE IF EXISTS books;

CREATE TABLE books (
    isbn VARCHAR(20) PRIMARY KEY,
    book_title VARCHAR(75),
    category VARCHAR(25),
    rental_price FLOAT,
    status VARCHAR(15),
    author VARCHAR(35),
    publisher VARCHAR(55)
);


-- Creating Members Table --

DROP TABLE IF EXISTS members;

CREATE TABLE members (
    member_id VARCHAR(10) PRIMARY KEY,
    member_name VARCHAR(20),
    member_address VARCHAR(20),
    reg_date DATE
);


-- Creating Issued Status Table --


DROP TABLE IF EXISTS issued_status;

CREATE TABLE issued_status (
    issued_id VARCHAR(10),
    issued_member_id VARCHAR(10), -- FK
    issued_book_name VARCHAR(75),
    issued_date DATE,
    issued_book_isbn VARCHAR(25), -- FK
    issued_emp_id VARCHAR(10)  -- FK
);


-- Creating Return Status Table --



DROP TABLE IF EXISTS return_status;

CREATE TABLE return_status (
    return_id VARCHAR(25) PRIMARY KEY,
    issued_id VARCHAR(25),
    return_book_name VARCHAR(75),
    return_date DATE,
    return_book_isbn VARCHAR(25)
);


```
## 📊 Entity-Relationship Diagram (ER Diagram)

### Overview:
This section explains the relationships between the key tables in the Library Management System. Here's how the entities are connected:

### ER Diagram:

![ER diagram for Library Management System](https://github.com/user-attachments/assets/10666826-6758-4a5d-a8f5-18703fa837d2)


1. **Members**:
   - `member_id` is the Primary Key (PK).
   - Connected to `issued_status` via `issued_member_id` (Foreign Key, FK).
2. **Books**:
   - `isbn` is the Primary Key (PK).
   - Linked to `issued_status` via `issued_book_isbn` (FK).
3. **Issued Status**:
   - Tracks books issued to members, linking `member_id` and `isbn`.
   - `issued_id` is the Primary Key (PK).
   - Connected to `return_status` via `issued_id` (FK).
4. **Return Status**:
   - Tracks books returned by members.
   - `return_id` is the Primary Key (PK).
   - Connected to `issued_status` via `issued_id` (FK).
5. **Employees**:
   - `emp_id` is the Primary Key (PK).
   - Linked to `issued_status` via `issued_emp_id` (FK).
   - Associated with `branch` via `branch_id`.
6. **Branch**:
   - `branch_id` is the Primary Key (PK).
   - Linked to `employees` via `branch_id`.
  

### 2. CRUD Operations

- **Create**: Inserted sample records into the `books` table.
- **Read**: Retrieved and displayed data from various tables.
- **Update**: Updated records in the `employees` table.
- **Delete**: Removed records from the `members` table as needed.

**Task 1. Create a New Book Record**
-- "978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.')"

```sql

INSERT INTO books (
    isbn, 
    book_title, 
    category, 
    rental_price, 
    status, 
    author, 
    publisher
) 
VALUES (
    '978-1-60129-456-2', 
    'To Kill a Mockingbird', 
    'Classic', 
    6.00, 
    'yes', 
    'Harper Lee', 
    'J.B. Lippincott & Co.'
);

-- checking whether it has been updated or not 

SELECT *
FROM books
WHERE isbn = '978-1-60129-456-2';


```
**Task 2: Update an Existing Member's Address**

```sql

UPDATE members
SET member_address = '125 Main St.'
WHERE member_id = 'C101';

SELECT *
FROM members;

```

**Task 3: Delete a Record from the Issued Status Table**
-- Objective: Delete the record with issued_id = 'IS121' from the issued_status table.

```sql

DELETE FROM issued_status
WHERE issued_id = 'IS121';

--(We are unable to delete records which are in reference with another table as foreign key)

```

**Task 4: Retrieve All Books Issued by a Specific Employee**
-- Objective: Select all books issued by the employee with emp_id = 'E101'.
```sql

SELECT * FROM issued_status
WHERE issued_emp_id = 'E101';

```


**Task 5: List Members Who Have Issued More Than One Book**
-- Objective: Use GROUP BY to find members who have issued more than one book.

```sql

SELECT 
    isd.issued_emp_id, 
    emp.emp_name, 
    COUNT(isd.issued_book_isbn) AS Total_book_issued
FROM 
    issued_status isd
JOIN 
    employees emp 
ON 
    isd.issued_emp_id = emp.emp_id
GROUP BY 
    isd.issued_emp_id, 
    emp.emp_name
HAVING 
    COUNT(isd.issued_book_isbn) > 1;

```

### 3.  (Create View As Select)

- **Task 6: Create Overdue Books View: Designed a SQL view to consolidate overdue book data, including member details, book titles, and overdue durations for improved query performance and insights.

```sql

CREATE VIEW issued_book_cnt AS 
SELECT 
    b.isbn, 
    b.book_title, 
    COUNT(isd.issued_book_isbn) AS Total_book_issued
FROM 
    issued_status isd
JOIN 
    books b
ON 
    isd.issued_book_isbn = b.isbn
GROUP BY 
    b.isbn, 
    b.book_title;


SELECT * 
FROM issued_book_cnt;

```


### 4. Data Analysis & Findings

The following SQL queries were used to address specific questions:

Task 7. **Retrieve All Books in a Specific Category**:

```sql

SELECT * 
FROM books
WHERE category = 'Classic';

```

8. **Task 8: Find Total Rental Income by Category**:

```sql

SELECT
    b.category,
    SUM(b.rental_price) AS Total_rental_income,
    COUNT(*) AS Total_books_issued
FROM 
    books AS b
JOIN 
    issued_status AS ist
ON 
    ist.issued_book_isbn = b.isbn
GROUP BY 
    b.category;

```

9. **List Members Who Registered in the Last 180 Days**:
```sql


--as this is a old record ,lets insert some new records 

INSERT INTO members(member_id, member_name, member_address, reg_date)
VALUES
('C200', 'sam', '145 Main St', '2025-04-24'),
('C201', 'john', '133 Main St', '2025-03-01');



select * from members
where reg_date >= current_DATE - INTERVAL '180 days';

```

10. **List Employees with Their Branch Manager's Name and their branch details**:

```sql

SELECT 
    e1.*, 
    e2.emp_name AS manager
FROM 
    employees e1
JOIN 
    branch b
ON 
    e1.branch_id = b.branch_id
JOIN 
    employees e2
ON 
    e2.emp_id = b.manager_id;

```

Task 11. **Create a VIEW of Books with Rental Price Above a Certain Threshold**:
```sql

CREATE VIEW premium_reads AS 
SELECT * 
FROM books 
WHERE rental_price > 7;

SELECT * 
FROM premium_reads;

```

Task 12: **Retrieve the List of Books Not Yet Returned**
```sql

SELECT DISTINCT 
    ist.issued_book_name
FROM 
    issued_status ist
LEFT JOIN 
    return_status rs
ON 
    ist.issued_id = rs.issued_id
WHERE 
    rs.return_id IS NULL;

```

## Advanced SQL Operations

**Task 13: Identify Members with Overdue Books**  
Write a query to identify members who have overdue books (assume a 30-day return period). Display the member's_id, member's name, book title, issue date, and days overdue.

```sql

SELECT 
    ist.issued_member_id AS Member_ID,  -- ID of the member who borrowed the book
    m.member_name AS Member_Name,       -- Name of the borrower
    b.book_title AS Book_Title,         -- Title of the borrowed book
    ist.issued_date AS Issue_Date,      -- Date when the book was issued
    CURRENT_DATE - ist.issued_date AS Days_Overdue  -- Number of days overdue
FROM 
    issued_status ist 
JOIN 
    members m 
    ON ist.issued_member_id = m.member_id   -- Joining to fetch member details
JOIN 
    books b 
    ON ist.issued_book_isbn = b.isbn        -- Joining to fetch book details
LEFT JOIN 
    return_status rs 
    ON ist.issued_id = rs.issued_id         -- Ensuring books are included even if not returned
WHERE 
    rs.return_id IS NULL                    -- Filters unreturned books only
    AND CURRENT_DATE - ist.issued_date > 30 -- Includes books issued for more than 30 days
ORDER BY 
    ist.issued_member_id;                   -- Sorts results by member ID


```


**Task 14: Update Book Status on Return**  
Write a query to update the status of books in the books table to "Yes" when they are returned (based on entries in the return_status table).


```sql


ALTER TABLE return_status
ADD COLUMN book_quality VARCHAR(15);

--Stored procedure

CREATE OR REPLACE PROCEDURE add_return_record(
    p_return_id VARCHAR(25),       -- Return ID input parameter
    p_issued_id VARCHAR(10),       -- Issued ID input parameter
    p_book_quality VARCHAR(15)     -- Book quality input parameter
)
LANGUAGE plpgsql
AS $$
DECLARE
    v_isbn VARCHAR(50);            -- Variable to store book ISBN
    v_book_name VARCHAR(75);       -- Variable to store book name
BEGIN
    -- Task 1: Insert data into return_status table
    INSERT INTO return_status (return_id, issued_id, return_date, book_quality)
    VALUES (p_return_id, p_issued_id, CURRENT_DATE, p_book_quality);

    -- Task 2: Retrieve book details from issued_status table
    SELECT issued_book_isbn, book_name
    INTO v_isbn, v_book_name
    FROM issued_status
    WHERE issued_id = p_issued_id;

    -- Task 3: Update the status of the book in the books table
    UPDATE books
    SET status = 'Yes',
        quality = p_book_quality      -- Updates the book quality as well
    WHERE isbn = v_isbn;

    -- Task 4: Raise a notice to confirm the return process
    RAISE NOTICE 'Thank you for returning the book: %', v_book_name;
END;
$$;

--testing function add_return_records()


DELETE FROM return_status 
WHERE return_id= 'RS138'

CALL add_return_record('RS138','IS135','Good');

```




**Task 15: Branch Performance Report**  
Create a query that generates a performance report for each branch, showing the number of books issued, the number of books returned, and the total revenue generated from book rentals.

```sql


CREATE TABLE branch_reports as 
SELECT 
    b.branch_id AS Branch_ID,                    -- ID of the branch
    COUNT(ist.issued_id) AS Books_Issued,       -- Total number of books issued by the branch
    COUNT(rs.return_id) AS Books_Returned,      -- Total number of books returned at the branch
    SUM(bo.rental_price) AS Total_Revenue       -- Total revenue generated from book rentals
FROM 
    branch b
JOIN 
    employees e 
    ON e.branch_id = b.branch_id                -- Joining employees table to link branches
JOIN 
    issued_status ist 
    ON ist.issued_emp_id = e.emp_id             -- Joining issued_status table for issued books
LEFT JOIN 
    return_status rs 
    ON ist.issued_id = rs.issued_id             -- Ensuring all issued books are included (even if not returned)
JOIN 
    books bo 
    ON ist.issued_book_isbn = bo.isbn           -- Fetching book details for rental price
GROUP BY 
    b.branch_id;                                -- Grouping data by branch ID to consolidate report


SELECT * 
FROM branch_reports;


```

**Task 16: Create Summary Tables**: Used CTAS to generate new tables based on query results  CTAS: Create a Table of Active Members**  
Use the CREATE TABLE AS (CTAS) statement to create a new table active_members containing members who have issued at least one book in the last 2 months.

```sql

CREATE TABLE active_members AS
SELECT * 
FROM members
WHERE member_id IN (
    SELECT DISTINCT m.member_id
    FROM members m
    JOIN issued_status ist
        ON m.member_id = ist.issued_member_id -- Match members with issued books
    WHERE issued_date >= CURRENT_DATE - INTERVAL '2 months' -- Filter by issue date
);

SELECT * FROM active_members;

```


**Task 17: Find Employees with the Most Book Issues Processed**  
Write a query to find the top 3 employees who have processed the most book issues. Display the employee name, number of books processed, and their branch.

```sql

SELECT 
    b.branch_id AS Branch_ID,                   -- Branch ID where the employee works
    e.emp_id AS Employee_ID,                   -- Employee's unique identifier
    e.emp_name AS Employee_Name,               -- Name of the employee
    COUNT(ist.issued_id) AS Total_Books_Issued -- Total number of books processed by the employee
FROM 
    employees e
JOIN 
    issued_status ist 
    ON ist.issued_emp_id = e.emp_id            -- Linking employees to issued books
JOIN 
    branch b 
    ON e.branch_id = b.branch_id               -- Linking employees to their branches
GROUP BY 
    b.branch_id, e.emp_id, e.emp_name          -- Grouping results by branch and employee
ORDER BY 
    Total_Books_Issued DESC                    -- Sorting by the number of books issued in descending order
LIMIT 3;                                       -- Limiting the output to top 3 employees


```

**Task 18: Identify Members Issuing High-Risk Books**  
Write a query to identify members who have issued books more than twice with the status "damaged" in the books table. Display the member name, book title, and the number of times they've issued damaged books.    
```sql

SELECT 
    m.member_name AS Member_Name,           -- Name of the member
    b.book_title AS Book_Title,             -- Title of the book issued
    COUNT(ist.issued_id) AS Times_Issued    -- Number of times the book was issued
FROM 
    members m
JOIN 
    issued_status ist 
    ON m.member_id = ist.issued_member_id   -- Linking members to issued books
JOIN 
    books b 
    ON ist.issued_book_isbn = b.isbn        -- Linking issued books to their titles
WHERE 
    b.status = 'damaged'                    -- Filtering books with status "damaged"
GROUP BY 
    m.member_name, b.book_title             -- Grouping by member name and book title
HAVING 
    COUNT(ist.issued_id) > 2                -- Members who issued books more than twice
ORDER BY 
    Times_Issued DESC;                      -- Sorting by the number of times books were issued (descending)

```

**Task 19: Stored Procedure**
Objective:
Create a stored procedure to manage the status of books in a library system.
Description:
Write a stored procedure that updates the status of a book in the library based on its issuance. The procedure should function as follows:
The stored procedure should take the book_id as an input parameter.
The procedure should first check if the book is available (status = 'yes').
If the book is available, it should be issued, and the status in the books table should be updated to 'no'.
If the book is not available (status = 'no'), the procedure should return an error message indicating that the book is currently not available.

```sql


CREATE OR REPLACE PROCEDURE issued_book(
    p_issued_id VARCHAR(10),           -- Issued ID input parameter
    p_issued_member_id VARCHAR(10),    -- Member ID who is issuing the book
    p_issued_book_isbn VARCHAR(25),    -- ISBN of the book being issued
    p_issued_emp_id VARCHAR(20)        -- Employee ID processing the issuance
)
LANGUAGE plpgsql
AS $$
DECLARE
    v_status VARCHAR(10);              -- Variable to hold the book's current status
BEGIN
    -- Step 1: Check if the book is available
    SELECT status INTO v_status 
    FROM books 
    WHERE isbn = p_issued_book_isbn;

    IF v_status = 'yes' THEN
        -- Step 2: Insert into issued_status if the book is available
        INSERT INTO issued_status (
            issued_id, issued_member_id, issued_date, issued_book_isbn, issued_emp_id
        )
        VALUES (
            p_issued_id, p_issued_member_id, CURRENT_DATE, p_issued_book_isbn, p_issued_emp_id
        );

        -- Step 3: Update the book's status to 'No' (issued)
        UPDATE books
        SET status = 'no'
        WHERE isbn = p_issued_book_isbn;

        -- Step 4: Notify successful issuance
        RAISE NOTICE 'Book record added successfully for ISBN: %', p_issued_book_isbn;

    ELSE
        -- Step 5: Notify unavailability
        RAISE NOTICE 'Sorry, the book with ISBN: % is currently unavailable.', p_issued_book_isbn;
    END IF;
END;
$$;


-- Testing The function
SELECT * FROM books;
-- "978-0-553-29698-2" -- yes
-- "978-0-375-41398-8" -- no
SELECT * FROM issued_status;

CALL issue_book('IS155', 'C108', '978-0-553-29698-2', 'E104');
CALL issue_book('IS156', 'C108', '978-0-375-41398-8', 'E104');

SELECT * FROM books
WHERE isbn = '978-0-375-41398-8'

```


**Task 20: Create Table As Select (CTAS)**
Objective: Create a CTAS (Create Table As Select) query to identify overdue books and calculate fines.

Description: Write a CTAS query to create a new table that lists each member and the books they have issued but not returned within 30 days. The table should include:
    The number of overdue books.
    The total fines, with each day's fine calculated at $0.50.
    The number of books issued by each member.
    The resulting table should show:
    Member ID
    Number of overdue books
    Total fines


```sql


CREATE TABLE overdue_books_fines AS
SELECT 
    m.member_id AS Member_ID,                    -- ID of the member
    COUNT(ist.issued_id) AS Overdue_Books,       -- Number of overdue books
    SUM(GREATEST((CURRENT_DATE - ist.issued_date - 30), 0) * 0.50) AS Total_Fines -- Deducting 30 days, and ensuring no negative fines
FROM 
    members m
JOIN 
    issued_status ist 
    ON ist.issued_member_id = m.member_id        -- Linking members with issued books
LEFT JOIN 
    return_status rs 
    ON rs.issued_id = ist.issued_id              -- Including books that have not been returned
WHERE 
    rs.return_id IS NULL                         -- Filtering books that are not returned
    AND CURRENT_DATE - ist.issued_date > 30      -- Overdue books (issued more than 30 days ago)
GROUP BY 
    m.member_id                                  -- Grouping results by member ID
ORDER BY 
    Total_Fines DESC;                            -- Sorting results by total fines in descending order

SELECT * 
FROM overdue_books_fines;

```



## Reports

- **Database Schema**: Detailed table structures and relationships.
- **Data Analysis**: Insights into book categories, employee salaries, member registration trends, and issued books.
- **Summary Reports**: Aggregated data on high-demand books and employee performance.

## Conclusion

This project demonstrates the application of SQL skills in creating and managing a library management system. It includes database setup, data manipulation, and advanced querying, providing a solid foundation for data management and analysis.


Thank you for your interest in this project!

