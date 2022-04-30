# SQL

## SQL Basics

SQL = Data Query Language (DQL) + Data Definition Language(DDL) + Data Control Language (DQL) + Data Manupalation Language (DML)

<br>

## Tables & Keys

| <ins >student id</ins> | <span style="color:lightblue">name</span> | <span style="color:lightblue">major</span> |
| ---------------------- | ----------------------------------------- | ------------------------------------------ |
| 1                      | Kate                                      | Science                                    |
| 2                      | Mike                                      | Maths                                      |
| 3                      | Claire                                    | English                                    |

**Primary key:** An attriubute which uniquely identify a row (underlined)<br>
**Foreign Key:** Primary key of another table (green)<br>
**Composity Key:** Two or more attribute to uniquely identify a row (red)

<br>

## Datatypes

| Datatype     | Description                                                                |
| ------------ | -------------------------------------------------------------------------- |
| INT          | Whole numbers                                                              |
| DECIMAL(M,N) | Decimal numbers M=no of digits before decimal N=No of digits after decimal |
| VARCHAR(1)   | string of text of length 1                                                 |
| BLOB         | Binary large object. Stores large datra                                    |
| DATE         | YYYY-MM-DD                                                                 |
| TIMESTAMP    | HH:MM:SS                                                                   |

<br>

## Creating Tables

```sql
CREATE TABLE student(
    student_id INT PRIMARY KEY,
    name VARCHAR(20),
    major VARCHAR(20),
)

DESCRIBE student;
DROP TABLE student;
ALTER TABLE student ADD gpa DECIMAL(3,2);
ALTER TABLE student DROP COLUMN gpa
```

<br>

## Inserting Data

```sql
INSERT INTO student VALUES(1, 'Jack', 'Biology');
INSERT INTO student (student_id, name) VALUES(2, 'Kate'
);
```

<br>

## Constraits

```sql
CREATE TABLE student(
    name VARCHAR(20) NOT NULL,
    major VARCHAR(20) UNIQUE DEFAULT 'undecided',
    student_id INT PRIMARY KEY AUTO_INCREMENT,
)
```

<br>

## Update

```sql
UPDATE student
SET major='Bio', name='Tom'
WHERE major='Biology';
```

<br>

## Delete

```sql
DELETE student
WHERE student-id=5;
```

<br>

## Basic Queries

```sql
SELECT student.name, student.major FROM student
ORDER BY name DESC
ORDER BY major, student_id
LIMIT 2
WHERE major = 'chemistry';

SELECT first_name AS forename, last_name AS surname
FROM employee;

SELECT DISTINCT sex
FROM employee;
```

You can combine different where clauses with OR, AND and BETWEEN

<br>

## Table Example

```sql
CREATE TABLE employee (
  emp_id INT PRIMARY KEY,
  first_name VARCHAR(40),
  last_name VARCHAR(40),
  birth_day DATE,
  sex VARCHAR(1),
  salary INT,
  super_id INT,
  branch_id INT
);

CREATE TABLE branch (
  branch_id INT PRIMARY KEY,
  branch_name VARCHAR(40),
  mgr_id INT,
  mgr_start_date DATE,
  FOREIGN KEY(mgr_id) REFERENCES employee(emp_id) ON DELETE SET NULL
);

ALTER TABLE employee
ADD FOREIGN KEY(branch_id) REFERENCES branch(branch_id) ON DELETE SET NULL;

ALTER TABLE employee
ADD FOREIGN KEY(super_id) REFERENCES employee(emp_id) ON DELETE SET NULL;

CREATE TABLE works_with (
  emp_id INT,
  client_id INT,
  total_sales INT,
  PRIMARY KEY(emp_id, client_id),
  FOREIGN KEY(emp_id) REFERENCES employee(emp_id) ON DELETE CASCADE,
  FOREIGN KEY(client_id) REFERENCES client(client_id) ON DELETE CASCADE
);

CREATE TABLE branch_supplier (
  branch_id INT,
  supplier_name VARCHAR(40),
  supply_type VARCHAR(40),
  PRIMARY KEY(branch_id, supplier_name),
  FOREIGN KEY(branch_id) REFERENCES branch(branch_id) ON DELETE CASCADE
);
```

<br>

## Functions

```sql
SELECT COUNT(emp_if) FROM employee;

SELECT AVG(salary) FROM employee;

SELECT COUNT(sex), sex
FROM employee;
GROUP BY sex;

SELECT SUM(total_sales), emp_id
FROM works_with
GROUP by emp_id;
```

<br>

## Wildcards

Anything that ends or starts with LLC

```sql
SELECT *
FROM client
WHERE client_name LIKE '%LLC%'
```

'\_' is one digit

```sql
SELECT *
FROM employee
WHERE birth_date LIKE '_____-10%'
```

[abc]% either a,b or c<br>
[a-f] a to f

<br>

## Union

```sql
SELECT first_name
FROM employee
UNION
SELECT branch_name
FROM branch;
```

Unions should have some number of columns<br>
They also need to be of the same datatype

<br>

## Joins

Joins is used to combine rows from two or more tables based on related colimns

```sql
SELECT employee.emp_id, employee.first_name, branch.branch_name
FROM employee
JOIN branch
ON employee.emp_id = branch.mgr_id;
```

**(INNER) JOINS:** Matching values in both table<br>
**LEFT (OUTER) JOIN:** Left table + matching<br>
**RIGHT (OUTER) JOIN:** Right table + matching<br>
**FULL (OUTER) JOIN:** Not in mySQL<br>

<br>

## Nested Queries

Names of employees that have sold over 30,000 to a single client

```sql
SELECT empolyee.first_name
FROM employee
WHERE employee.emp_id IN (
    SELECT works_with.emp_id
    FROM works_with
    WHERE works_with.total_sales > 30000
);
```

All clients who are handled by branch managed by emp_id 102

```sql
SELECT client_name
FROM client
WHERE client.branch_id = (
    SELECT branch.branch_id
    FROM branch
    WHERE branch.mgr_id = 102
)
```
