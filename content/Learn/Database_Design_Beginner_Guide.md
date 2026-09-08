# Database Design — Beginner to Practical Guide

> A beginner-friendly guide to designing relational databases correctly.
>
> Examples use **MySQL-style SQL**, but the design concepts apply to PostgreSQL and most relational databases too.

---

# 1. What Is Database Design?

Database design is the process of deciding:

- What data your application needs to store
- How that data should be organized
- Which pieces of data belong together
- How different pieces of data are connected
- Which rules should be enforced by the database
- How the database should retrieve data efficiently

A poorly designed database can cause:

- Duplicate data
- Inconsistent data
- Difficult queries
- Accidental deletion of important information
- Slow application performance
- Hard-to-maintain code

A well-designed database makes your application:

- Easier to understand
- Easier to query
- More reliable
- More scalable
- Easier to change

---

# 2. Start With a Real-World Example

Suppose we are building an online shopping application.

We need to store:

- Customers
- Products
- Orders
- Payments
- Order items

A beginner may try to put everything into one table:

```text
orders

order_id
customer_name
customer_email
product_name
product_price
quantity
payment_method
payment_status
```

This looks simple, but it creates problems.

For example, suppose one order contains 3 products:

```text
order_id | customer_name | product_name | product_price | quantity
---------+---------------+--------------+---------------+---------
101      | Teja          | Laptop       | 70000         | 1
101      | Teja          | Mouse        | 1000          | 2
101      | Teja          | Keyboard     | 2000          | 1
```

Customer information is repeated.

The order information is repeated.

Product information is repeated.

This is one of the problems database design tries to solve.

Instead, we can separate the concepts into tables:

```text
customers
products
orders
order_items
payments
```

The tables are then connected using relationships and keys.

---

# 3. Modeling

Database modeling means creating a conceptual representation of the data before implementing it as tables.

The three important ideas are:

1. Entities
2. Relationships
3. ER diagrams

---

# 4. Entities

## 4.1 What Is an Entity?

An **entity** is a thing about which we want to store information.

Think of an entity as a noun in your application's requirements.

Examples:

```text
Student
Teacher
Course
Employee
Department
Customer
Product
Order
Payment
```

For an e-commerce application:

```text
Customer
Product
Order
Payment
```

are entities.

---

## 4.2 Entity vs Table

Conceptually:

```text
Entity = real-world thing/concept
Table  = database representation of that entity
```

For example:

```text
Entity:
Customer

Database table:
customers
```

The entity is the design concept.

The table is how we implement it.

---

# 5. Attributes

An entity has properties called **attributes**.

For example:

```text
Customer

customer_id
name
email
phone
date_of_birth
```

Here:

```text
Customer = Entity

customer_id
name
email
phone
date_of_birth
= Attributes
```

In a relational database, attributes usually become columns.

Example:

```sql
CREATE TABLE customers (
    customer_id INT PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(255),
    phone VARCHAR(20)
);
```

---

# 6. Choosing Entities

A common beginner mistake is creating entities based only on every noun that appears in a requirement.

Instead, ask:

> "Does this thing need its own identity and data?"

For example:

> Customers can place orders. Each order can contain multiple products.

Likely entities:

```text
Customer
Order
Product
```

But "place" is not an entity.

It describes a relationship:

```text
Customer places Order
```

---

# 7. Relationships

A **relationship** describes how entities are connected.

Examples:

```text
Customer places Order
Customer owns Account
Student enrolls in Course
Employee works in Department
Doctor treats Patient
```

Think of relationships as verbs.

```text
Customer ---- places ----> Order
Student ---- enrolls in ----> Course
Employee ---- works in ----> Department
```

---

# 8. ER Diagrams

ER stands for:

> **Entity-Relationship**

An ER diagram visually represents:

- Entities
- Attributes
- Relationships
- Cardinality

A simple ER diagram might look like:

```text
+-------------+              +-------------+
|  CUSTOMER   |              |    ORDER    |
+-------------+              +-------------+
| customer_id |              | order_id    |
| name        |              | order_date  |
| email       |              | customer_id |
+-------------+              +-------------+
        |                            |
        |         places             |
        +----------------------------+
```

A more formal representation:

```text
CUSTOMER 1 ----------- N ORDER
```

This means:

> One customer can have many orders.

---

# 9. Cardinality

Cardinality describes how many records of one entity can be associated with another entity.

The three main types are:

```text
1 : 1
1 : N
M : N
```

These correspond to:

- One-to-One
- One-to-Many
- Many-to-Many

We will study each in detail later.

---

# 10. Keys

Keys are extremely important in relational databases.

A key helps the database:

- Identify rows
- Connect tables
- Prevent duplicates
- Enforce relationships

The major keys in this guide are:

- Primary Key
- Foreign Key
- Composite Key
- Unique Key

---

# 11. Primary Key

## 11.1 What Is a Primary Key?

A **primary key** uniquely identifies each row in a table.

Example:

```text
students

student_id | name
-----------+---------
1          | Teja
2          | Rahul
3          | Priya
```

`student_id` is a good primary key because every student has a different value.

```sql
CREATE TABLE students (
    student_id INT PRIMARY KEY,
    name VARCHAR(100)
);
```

---

## 11.2 Rules of a Primary Key

A primary key must be:

1. Unique
2. Not NULL

Example:

```text
student_id
----------
1
2
3
```

Invalid:

```text
student_id
----------
1
1
2
```

because `1` appears twice.

Also invalid:

```text
student_id
----------
1
NULL
2
```

because a primary key cannot be NULL.

---

# 12. Natural Key vs Surrogate Key

There are two common approaches to choosing identifiers.

## Natural Key

A natural key comes from real-world data.

Examples:

```text
email
phone_number
passport_number
ISBN
```

For example:

```sql
email VARCHAR(255) PRIMARY KEY
```

could theoretically identify a customer.

But natural keys can change.

A user might change their email address.

---

## Surrogate Key

A surrogate key is an artificial identifier created specifically for the database.

Example:

```sql
customer_id INT PRIMARY KEY AUTO_INCREMENT
```

Values might be:

```text
1
2
3
4
```

This is extremely common.

A typical design is:

```sql
CREATE TABLE customers (
    customer_id INT PRIMARY KEY AUTO_INCREMENT,
    email VARCHAR(255) UNIQUE NOT NULL,
    name VARCHAR(100) NOT NULL
);
```

Here:

```text
customer_id = Primary Key
email       = Unique business attribute
```

---

# 13. AUTO_INCREMENT

In MySQL, `AUTO_INCREMENT` automatically generates a new number when a row is inserted.

Example:

```sql
CREATE TABLE customers (
    customer_id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL
);
```

Then:

```sql
INSERT INTO customers (name)
VALUES ('Teja');

INSERT INTO customers (name)
VALUES ('Rahul');
```

might produce:

```text
customer_id | name
------------+-------
1           | Teja
2           | Rahul
```

The application does not need to manually supply the ID.

---

# 14. Important Primary Key Mistake

A table can have only **one primary key constraint**.

This is invalid:

```sql
CREATE TABLE students (
    student_id INT PRIMARY KEY,
    email VARCHAR(255) PRIMARY KEY
);
```

It causes:

```text
Multiple primary key defined
```

However, one primary key can contain multiple columns.

That is called a:

> Composite Primary Key

We will see that later.

---

# 15. Foreign Key

A **foreign key** connects one table to another.

Suppose we have:

```text
customers
---------
customer_id
name
```

and:

```text
orders
------
order_id
customer_id
order_date
```

`orders.customer_id` refers to `customers.customer_id`.

Example:

```sql
CREATE TABLE customers (
    customer_id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL
);

CREATE TABLE orders (
    order_id INT PRIMARY KEY AUTO_INCREMENT,
    customer_id INT NOT NULL,
    order_date DATE NOT NULL,

    FOREIGN KEY (customer_id)
        REFERENCES customers(customer_id)
);
```

---

# 16. Why Foreign Keys Matter

Suppose this customer exists:

```text
customer_id = 10
```

Then this order is valid:

```text
order_id | customer_id
---------+------------
101      | 10
```

But this might be invalid:

```text
order_id | customer_id
---------+------------
102      | 9999
```

if customer `9999` does not exist.

The foreign key allows the database to enforce this relationship.

This protects **referential integrity**.

---

# 17. Referential Integrity

Referential integrity means:

> A foreign key should refer to a valid row in the referenced table, according to the defined foreign-key rules.

Example:

```text
customers
---------
1 | Teja
2 | Rahul
```

```text
orders
------
101 | 1
102 | 2
```

Good.

But:

```text
103 | 500
```

is not valid if customer `500` does not exist and the FK does not permit such a state.

---

# 18. Foreign Key Direction

Consider:

```text
Customer 1 ---- N Order
```

Which table gets the foreign key?

The **many side** normally stores the foreign key.

Therefore:

```text
customers
---------
customer_id

orders
------
order_id
customer_id  <-- FK
```

This is one of the most important rules in relational database design.

---

# 19. Composite Key

A composite key uses multiple columns together to uniquely identify a row.

Example:

Suppose students can enroll in courses.

```text
student_id | course_id
-----------+----------
1          | 101
1          | 102
2          | 101
```

Neither `student_id` nor `course_id` is unique by itself.

But the combination:

```text
(student_id, course_id)
```

can be unique.

We can define:

```sql
CREATE TABLE enrollments (
    student_id INT NOT NULL,
    course_id INT NOT NULL,

    PRIMARY KEY (student_id, course_id),

    FOREIGN KEY (student_id)
        REFERENCES students(student_id),

    FOREIGN KEY (course_id)
        REFERENCES courses(course_id)
);
```

---

# 20. Composite Key vs Single-Column Primary Key

Single-column key:

```text
PRIMARY KEY (student_id)
```

Composite key:

```text
PRIMARY KEY (student_id, course_id)
```

The important idea is:

```text
student_id alone       -> not unique
course_id alone        -> not unique
student_id + course_id -> unique
```

---

# 21. Unique Key / UNIQUE Constraint

A `UNIQUE` constraint prevents duplicate values.

Example:

```sql
CREATE TABLE users (
    user_id INT PRIMARY KEY AUTO_INCREMENT,
    email VARCHAR(255) UNIQUE
);
```

This means two users cannot have the same email, subject to the database's handling of NULLs.

For example:

```text
user_id | email
--------+-------------------
1       | teja@example.com
2       | rahul@example.com
```

Allowed.

But:

```text
3 | teja@example.com
```

would violate the unique constraint.

---

# 22. PRIMARY KEY vs UNIQUE

Both enforce uniqueness, but they serve different roles.

## Primary Key

The primary identity of the row.

```sql
user_id INT PRIMARY KEY
```

Rules:

- Only one primary key constraint per table
- Cannot be NULL
- Commonly referenced by foreign keys

## UNIQUE

A separate uniqueness rule.

```sql
email VARCHAR(255) UNIQUE
```

A table can have multiple unique constraints.

Example:

```sql
CREATE TABLE users (
    user_id INT PRIMARY KEY,
    email VARCHAR(255) UNIQUE,
    username VARCHAR(100) UNIQUE
);
```

---

# 23. One-to-One Relationship

A one-to-one relationship means:

> One row in A is associated with at most one row in B, and vice versa.

Example:

```text
Person 1 -------- 1 Passport
```

A person has one passport record.

A passport belongs to one person.

---

# 24. Implementing One-to-One

Suppose:

```text
persons
-------
person_id
name

passports
---------
passport_id
passport_number
person_id
```

We can make `person_id` both:

- A foreign key
- UNIQUE

Example:

```sql
CREATE TABLE persons (
    person_id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL
);

CREATE TABLE passports (
    passport_id INT PRIMARY KEY AUTO_INCREMENT,
    passport_number VARCHAR(50) NOT NULL UNIQUE,
    person_id INT NOT NULL UNIQUE,

    FOREIGN KEY (person_id)
        REFERENCES persons(person_id)
);
```

Why `UNIQUE`?

Without it, one person could have multiple passport rows:

```text
passport_id | person_id
------------+----------
1           | 10
2           | 10
```

The unique constraint prevents that.

---

# 25. Why Use One-to-One?

One-to-one relationships are useful when:

- Data is optional
- Data is large
- Data has different security/access requirements
- Different parts of the application change independently

Example:

```text
users
-----
user_id
username

user_profiles
-------------
user_id
bio
profile_picture
address
```

A profile might be optional.

---

# 26. One-to-Many Relationship

This is probably the most common relationship.

It means:

> One row in A can have many rows in B.

Example:

```text
Customer 1 -------- N Order
```

One customer can place many orders.

But each order belongs to one customer.

---

# 27. Implementing One-to-Many

```sql
CREATE TABLE customers (
    customer_id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL
);

CREATE TABLE orders (
    order_id INT PRIMARY KEY AUTO_INCREMENT,
    customer_id INT NOT NULL,

    FOREIGN KEY (customer_id)
        REFERENCES customers(customer_id)
);
```

The foreign key is on the **many side**:

```text
orders.customer_id
```

---

# 28. Another One-to-Many Example

```text
Department 1 -------- N Employee
```

One department has many employees.

Each employee belongs to one department.

Tables:

```text
departments
-----------
department_id
name

employees
---------
employee_id
name
department_id
```

SQL:

```sql
CREATE TABLE departments (
    department_id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL
);

CREATE TABLE employees (
    employee_id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    department_id INT NOT NULL,

    FOREIGN KEY (department_id)
        REFERENCES departments(department_id)
);
```

---

# 29. Many-to-Many Relationship

A many-to-many relationship means:

> Many rows in A can be associated with many rows in B.

Example:

```text
Student N -------- M Course
```

A student can take many courses.

A course can have many students.

---

# 30. Why We Need a Junction Table

Relational databases normally implement many-to-many relationships using an intermediate table.

For example:

```text
students
courses
enrollments
```

The relationship becomes:

```text
Student 1 ---- N Enrollment N ---- 1 Course
```

Visualized:

```text
+----------+       +-------------+       +---------+
| STUDENTS | 1   N | ENROLLMENTS | N   1 | COURSES |
+----------+-------+-------------+-------+---------+
| student_id       | student_id           | course_id
| name             | course_id            | name
+----------+       +-------------+       +---------+
```

---

# 31. Many-to-Many SQL Example

```sql
CREATE TABLE students (
    student_id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL
);

CREATE TABLE courses (
    course_id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL
);

CREATE TABLE enrollments (
    student_id INT NOT NULL,
    course_id INT NOT NULL,

    PRIMARY KEY (student_id, course_id),

    FOREIGN KEY (student_id)
        REFERENCES students(student_id),

    FOREIGN KEY (course_id)
        REFERENCES courses(course_id)
);
```

This prevents the same student from being enrolled in the same course twice.

---

# 32. How to Recognize Relationship Types

Ask these questions.

## One-to-One

> How many B records can one A have?

Answer:

```text
At most 1
```

And:

> How many A records can one B have?

Answer:

```text
At most 1
```

---

## One-to-Many

Ask:

> How many B records can one A have?

Answer:

```text
Many
```

Then:

> How many A records can one B have?

Answer:

```text
One
```

---

## Many-to-Many

Ask:

> How many B records can one A have?

Answer:

```text
Many
```

And:

> How many A records can one B have?

Answer:

```text
Many
```

---

# 33. Optional vs Mandatory Relationships

Relationships are not always mandatory.

Example:

```text
Customer 1 ---- N Order
```

We might require every order to belong to a customer.

Then:

```sql
customer_id INT NOT NULL
```

But suppose an employee may or may not belong to a department.

Then:

```sql
department_id INT NULL
```

could be valid.

Example:

```sql
CREATE TABLE employees (
    employee_id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    department_id INT NULL,

    FOREIGN KEY (department_id)
        REFERENCES departments(department_id)
);
```

This allows an employee without a department.

---

# 34. Normalization

Normalization is one of the most important database design concepts.

The basic goal is:

> Reduce unnecessary duplication and prevent data anomalies.

Consider this table:

```text
orders

order_id
customer_name
customer_email
product_name
product_price
quantity
```

Suppose the same customer places 100 orders.

Then their name and email may be repeated 100 times.

Suppose the customer changes their email.

You would need to update many rows.

This creates problems.

---

# 35. Data Anomalies

Poorly designed tables can create three major anomalies:

1. Update anomaly
2. Insert anomaly
3. Delete anomaly

---

# 36. Update Anomaly

Suppose:

```text
order_id | customer_id | customer_email
---------+-------------+---------------------
101      | 1           | old@example.com
102      | 1           | old@example.com
103      | 1           | old@example.com
```

The customer changes their email.

If you update only row `101`:

```text
101 | 1 | new@example.com
102 | 1 | old@example.com
103 | 1 | old@example.com
```

Now the database contains conflicting information.

This is an **update anomaly**.

---

# 37. Insert Anomaly

Suppose customer data exists only inside an order table:

```text
orders

order_id
customer_name
customer_email
product_name
```

What if a new customer registers but has not placed an order?

Where do you store them?

You might be forced to create an incomplete order row.

This is an **insert anomaly**.

---

# 38. Delete Anomaly

Suppose:

```text
order_id | customer_name
---------+-------------
101      | Teja
```

If we delete the only order for Teja, we may accidentally lose the only stored information about the customer.

This is a **delete anomaly**.

---

# 39. First Normal Form — 1NF

A table should contain atomic values and avoid repeating groups.

Bad example:

```text
student_id | courses
-----------+-------------------------
1          | Java, SQL, Python
```

The `courses` column contains multiple values.

Better:

```text
student_id | course
-----------+---------
1          | Java
1          | SQL
1          | Python
```

Or, better still, use a proper relationship:

```text
students
courses
enrollments
```

---

# 40. Second Normal Form — 2NF

2NF is mainly relevant when a table has a **composite key**.

A table is in 2NF when:

- It is already in 1NF
- Every non-key attribute depends on the entire primary key, not just part of it

Consider:

```text
enrollments

student_id
course_id
student_name
course_name
grade
```

Primary key:

```text
(student_id, course_id)
```

But:

```text
student_name
```

depends only on:

```text
student_id
```

And:

```text
course_name
```

depends only on:

```text
course_id
```

They do not depend on the entire composite key.

Therefore the design violates 2NF.

---

# 41. Fixing the 2NF Example

Separate the entities:

```text
students
--------
student_id
student_name

courses
-------
course_id
course_name

enrollments
-----------
student_id
course_id
grade
```

Now:

```text
student_name -> students
course_name  -> courses
grade        -> enrollments
```

This is much cleaner.

---

# 42. Third Normal Form — 3NF

A table is in 3NF when it is in 2NF and non-key attributes do not depend on other non-key attributes.

Consider:

```text
employees

employee_id
employee_name
department_id
department_name
```

Primary key:

```text
employee_id
```

We have:

```text
employee_id -> department_id
department_id -> department_name
```

Therefore:

```text
employee_id -> department_name
```

indirectly.

`department_name` is really a property of the department, not the employee.

---

# 43. Fixing the 3NF Example

Separate the department:

```text
employees
---------
employee_id
employee_name
department_id

departments
-----------
department_id
department_name
```

Now:

```text
employees.department_id
        |
        v
departments.department_id
```

This removes unnecessary duplication.

---

# 44. Practical Understanding of Normalization

For a beginner, remember this:

> Store each fact in the place where that fact belongs.

Examples:

```text
Customer information
    -> customers

Product information
    -> products

Order information
    -> orders

Which products are in an order
    -> order_items
```

Don't repeat the same fact unnecessarily across unrelated rows.

---

# 45. Normalization Does Not Mean "Split Everything"

Over-normalization can also make a system difficult to work with.

You might end up with too many tiny tables and complicated queries.

The goal is not:

> "Create the maximum possible number of tables."

The goal is:

> "Create a logically correct structure with minimal unnecessary redundancy."

---

# 46. Indexes

After designing tables correctly, we need to think about performance.

An **index** helps the database find rows faster.

Without an appropriate index, a database may need to scan many rows.

Imagine:

```text
users
-----
1
2
3
...
10,000,000
```

Query:

```sql
SELECT *
FROM users
WHERE email = 'teja@example.com';
```

An index on `email` can make this lookup much more efficient.

---

# 47. How an Index Works Conceptually

Without an index:

```text
Search:

Row 1
Row 2
Row 3
Row 4
...
Row 10,000,000
```

The database may examine a huge number of rows.

With an index, the database maintains an additional data structure that helps locate matching rows.

Think of a book.

Without an index:

> Open every page until you find "Normalization".

With an index:

> Look at the index, find the page, go there directly.

This analogy is not exact internally, but it is useful for understanding the purpose.

---

# 48. Creating an Index

```sql
CREATE INDEX idx_users_email
ON users(email);
```

Then:

```sql
SELECT *
FROM users
WHERE email = 'teja@example.com';
```

can potentially benefit from the index.

---

# 49. Primary Keys and Indexes

Primary keys are normally indexed by the database.

For example:

```sql
CREATE TABLE users (
    user_id INT PRIMARY KEY,
    name VARCHAR(100)
);
```

The database generally maintains an index supporting the primary key.

You normally do not create another redundant index on the exact same primary-key column just because it is a primary key.

---

# 50. Unique Constraints and Indexes

A unique constraint also generally requires an index or index-like structure to enforce uniqueness efficiently.

Example:

```sql
email VARCHAR(255) UNIQUE
```

This is both a data-integrity rule and a useful lookup path.

---

# 51. Foreign Key Indexes

Foreign key columns are commonly used in joins.

Example:

```sql
orders.customer_id
```

and query:

```sql
SELECT *
FROM orders
WHERE customer_id = 10;
```

An index on the foreign key can be useful.

Depending on the database engine/version, some foreign-key index behavior may be automatic or may need to be created explicitly. You should understand what your chosen DBMS does rather than assuming.

Example explicit index:

```sql
CREATE INDEX idx_orders_customer_id
ON orders(customer_id);
```

---

# 52. Index Selection

Do not create an index on every column.

Indexes have costs:

- They consume disk space
- Inserts can become more expensive
- Updates can become more expensive
- Deletes can become more expensive
- Too many indexes increase maintenance overhead

The question should be:

> "Which columns are frequently used to find, join, sort, or filter data?"

---

# 53. Good Index Candidates

Common candidates include columns frequently used in:

```sql
WHERE
JOIN
ORDER BY
GROUP BY
```

Example:

```sql
SELECT *
FROM orders
WHERE customer_id = 10;
```

Potential index:

```sql
CREATE INDEX idx_orders_customer_id
ON orders(customer_id);
```

---

# 54. Selectivity

A useful concept is **selectivity**.

Suppose a table has 1,000,000 rows.

Column A:

```text
gender

Male
Female
```

Only two values.

Column B:

```text
email

1,000,000 mostly distinct values
```

An index on email is usually much more selective than an index on gender.

Why?

Because:

```text
WHERE email = 'x@example.com'
```

may find 1 row.

While:

```text
WHERE gender = 'Male'
```

may find hundreds of thousands of rows.

However, whether an index is useful depends on the actual workload and query plan. Do not choose indexes based on selectivity alone.

---

# 55. Composite Index

You can create an index on multiple columns:

```sql
CREATE INDEX idx_orders_customer_date
ON orders(customer_id, order_date);
```

This can help queries such as:

```sql
SELECT *
FROM orders
WHERE customer_id = 10
  AND order_date >= '2026-01-01';
```

The order of columns matters.

For a composite index:

```text
(customer_id, order_date)
```

is not equivalent to:

```text
(order_date, customer_id)
```

in terms of how the index can be used efficiently.

---

# 56. The Leftmost-Prefix Idea

Suppose:

```sql
CREATE INDEX idx_orders_customer_date
ON orders(customer_id, order_date);
```

This index is naturally useful for queries involving the leftmost column:

```sql
WHERE customer_id = 10
```

and:

```sql
WHERE customer_id = 10
  AND order_date >= '2026-01-01'
```

But a query using only:

```sql
WHERE order_date >= '2026-01-01'
```

may not benefit in the same way.

Exact index behavior depends on the database optimizer and query.

---

# 57. Don't Guess About Indexes

Use the query plan.

For example, MySQL:

```sql
EXPLAIN
SELECT *
FROM orders
WHERE customer_id = 10;
```

The execution plan can show whether the database is using an index and how it expects to execute the query.

Database performance should be measured, not guessed.

---

# 58. Constraints

Constraints are rules enforced by the database.

They are extremely important because application code is not the only thing interacting with a database.

Common constraints include:

```text
PRIMARY KEY
FOREIGN KEY
UNIQUE
NOT NULL
CHECK
DEFAULT
```

---

# 59. NOT NULL

`NOT NULL` means a value is required.

Example:

```sql
name VARCHAR(100) NOT NULL
```

This is invalid:

```sql
INSERT INTO users (name)
VALUES (NULL);
```

because `name` cannot be NULL.

---

# 60. DEFAULT

A default value is used when no value is supplied.

Example:

```sql
status VARCHAR(20) DEFAULT 'pending'
```

Then:

```sql
INSERT INTO orders (customer_id)
VALUES (10);
```

may produce:

```text
status = 'pending'
```

Example:

```sql
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
```

---

# 61. CHECK

A `CHECK` constraint enforces a condition.

Example:

```sql
CREATE TABLE products (
    product_id INT PRIMARY KEY,
    price DECIMAL(10,2) NOT NULL,
    CHECK (price >= 0)
);
```

This prevents negative prices.

Another example:

```sql
age INT CHECK (age >= 0)
```

Database support/details for `CHECK` constraints vary by DBMS and version, but modern MySQL and PostgreSQL support them.

---

# 62. Foreign Key Actions

Foreign keys can define what happens when referenced rows are updated or deleted.

Common options:

```text
CASCADE
SET NULL
RESTRICT
NO ACTION
```

Example:

```sql
FOREIGN KEY (customer_id)
REFERENCES customers(customer_id)
ON DELETE CASCADE
```

---

# 63. ON DELETE CASCADE

Suppose:

```text
Customer 10
   |
   +-- Order 101
   +-- Order 102
```

If the customer is deleted and the relationship uses:

```sql
ON DELETE CASCADE
```

the related orders can also be deleted.

This can be convenient.

But it can also be dangerous.

Never use `CASCADE` without understanding exactly what data will be removed.

---

# 64. ON DELETE SET NULL

Suppose an employee may exist without a department.

```sql
department_id INT NULL
```

Then:

```sql
FOREIGN KEY (department_id)
REFERENCES departments(department_id)
ON DELETE SET NULL
```

can mean:

> If the referenced department is deleted, set the employee's department_id to NULL.

This preserves the employee while removing the relationship.

---

# 65. RESTRICT / NO ACTION

These generally prevent deletion/update when doing so would violate the relationship.

Example:

```sql
FOREIGN KEY (customer_id)
REFERENCES customers(customer_id)
ON DELETE RESTRICT
```

This effectively says:

> You cannot delete this customer while dependent rows still exist.

Exact behavior and timing can vary by database engine.

---

# 66. Constraints vs Application Validation

Suppose your application checks:

```text
email cannot be NULL
```

That is useful.

But the database should often enforce it too:

```sql
email VARCHAR(255) NOT NULL
```

Why?

Because data can be modified through:

- Web applications
- Mobile apps
- Admin tools
- Scripts
- ETL jobs
- Background services
- Direct SQL

The database is the final guardian of its own data integrity.

---

# 67. Transactions

A **transaction** is a group of database operations treated as one logical unit.

Example:

Transfer money from Account A to Account B.

You need:

```text
1. Subtract money from A
2. Add money to B
```

You don't want this:

```text
A: -100
B: unchanged
```

because the money disappeared.

Both operations should succeed together, or neither should be committed.

That is what a transaction helps provide.

---

# 68. Basic Transaction Syntax

```sql
START TRANSACTION;

UPDATE accounts
SET balance = balance - 100
WHERE account_id = 1;

UPDATE accounts
SET balance = balance + 100
WHERE account_id = 2;

COMMIT;
```

If something goes wrong:

```sql
ROLLBACK;
```

---

# 69. COMMIT

`COMMIT` permanently makes the transaction's changes visible according to the database's transaction rules.

Example:

```sql
START TRANSACTION;

UPDATE accounts
SET balance = balance - 100
WHERE account_id = 1;

UPDATE accounts
SET balance = balance + 100
WHERE account_id = 2;

COMMIT;
```

After `COMMIT`, the changes are committed.

---

# 70. ROLLBACK

`ROLLBACK` cancels uncommitted changes in the current transaction.

Example:

```sql
START TRANSACTION;

UPDATE accounts
SET balance = balance - 100
WHERE account_id = 1;

ROLLBACK;
```

The subtraction is undone, assuming it was part of the transaction and not already committed.

---

# 71. ACID

ACID describes four important transaction properties:

```text
A = Atomicity
C = Consistency
I = Isolation
D = Durability
```

Let's understand each.

---

# 72. Atomicity

Atomicity means:

> A transaction is treated as an all-or-nothing unit.

Example:

```text
Transfer ₹100

1. Subtract ₹100 from A  ✓
2. Add ₹100 to B        ✗
```

If the transaction fails:

```text
Rollback
```

so the completed part is not left behind as a partial transfer.

Conceptually:

```text
Everything succeeds
OR
Nothing succeeds
```

---

# 73. Consistency

Consistency means:

> A successful transaction moves the database from one valid state to another valid state, preserving applicable constraints and rules.

Suppose:

```text
balance >= 0
```

is a business/database rule.

A transaction should not leave the database violating enforced rules.

Consistency depends on:

- Constraints
- Data types
- Referential integrity
- Application/business rules
- Transaction behavior

It is broader than simply "the data looks correct."

---

# 74. Isolation

Isolation means:

> Concurrent transactions should behave according to the database's isolation guarantees, preventing inappropriate interference between them.

Imagine:

```text
Transaction A
Transaction B
```

running at the same time.

Without proper concurrency control, one transaction might observe intermediate or conflicting data.

Database systems provide isolation levels to control this.

---

# 75. Common Isolation Levels

Common SQL isolation levels are:

```text
READ UNCOMMITTED
READ COMMITTED
REPEATABLE READ
SERIALIZABLE
```

Some systems also expose:

```text
SNAPSHOT
```

or implement similar behavior using MVCC.

---

# 76. Read Phenomena

To understand isolation, know these terms:

## Dirty Read

A transaction reads data written by another transaction that has not committed.

Example:

```text
A writes 500
B reads 500
A rolls back
```

B read a value that never became committed state.

---

## Non-Repeatable Read

Within one transaction:

```text
First read  = 500
Another transaction changes it
Second read = 300
```

The same query returns a different committed value within the transaction.

---

## Phantom Read

A query returns a set of rows, another transaction inserts/deletes matching rows, and repeating the query sees a different set.

Example:

First:

```sql
SELECT *
FROM orders
WHERE customer_id = 10;
```

returns:

```text
2 rows
```

Later in the same logical transaction, matching rows may become:

```text
3 rows
```

depending on the isolation semantics and query.

---

# 77. Isolation Levels — Simplified Mental Model

A simplified mental model:

```text
READ UNCOMMITTED
    least restrictive

READ COMMITTED
    stronger

REPEATABLE READ
    stronger

SERIALIZABLE
    strongest general isolation
```

Higher isolation can reduce concurrency or increase waiting/locking overhead.

There is no universally best level.

The right level depends on the application's consistency requirements and the DBMS.

---

# 78. Durability

Durability means:

> Once a transaction is committed, the database should preserve the committed result even after failures, according to its durability guarantees.

For example:

```text
COMMIT
Power failure
Database restarts
```

The committed data should still be present.

Actual durability depends on the database's storage and configuration.

---

# 79. Putting ACID Together

Imagine a bank transfer:

```text
Account A: 1000
Account B: 500
```

Transfer 200:

```text
A -> 800
B -> 700
```

ACID helps ensure:

### Atomicity

Both balance changes happen together.

### Consistency

Rules such as valid balances and foreign keys remain satisfied.

### Isolation

Concurrent transfers don't produce an invalid result because transactions interfere incorrectly.

### Durability

Once committed, the transfer survives a database restart/failure as supported by the system.

---

# 80. A Complete E-Commerce Example

Let's combine everything.

Entities:

```text
Customer
Product
Order
OrderItem
Payment
```

Relationships:

```text
Customer 1 ---- N Order

Order 1 ---- N OrderItem

Product 1 ---- N OrderItem

Order 1 ---- N Payment
```

Conceptually, `OrderItem` resolves:

```text
Order N ---- M Product
```

because an order can contain many products and a product can appear in many orders.

---

# 81. E-Commerce ER Model

```text
CUSTOMER
---------
customer_id PK
name
email UNIQUE

      1
      |
      | places
      |
      N

ORDER
-----
order_id PK
customer_id FK
order_date
status

      1
      |
      N

ORDER_ITEM
----------
order_id PK, FK
product_id PK, FK
quantity
unit_price

      N
      |
      |
      1

PRODUCT
-------
product_id PK
name
price
```

And:

```text
ORDER 1 ---- N PAYMENT
```

---

# 82. Why OrderItem Exists

Do not put this directly into `orders`:

```text
product_1
product_2
product_3
```

That would create repeating groups.

Instead:

```text
orders
------
order_id
customer_id

order_items
-----------
order_id
product_id
quantity
unit_price
```

Example:

```text
orders

order_id | customer_id
---------+------------
1001     | 5
```

```text
order_items

order_id | product_id | quantity | unit_price
---------+------------+----------+-----------
1001     | 10         | 1        | 70000
1001     | 20         | 2        | 1000
1001     | 30         | 1        | 2000
```

This is a proper one-to-many design.

---

# 83. Why Store unit_price in OrderItem?

Suppose a product currently costs:

```text
₹70,000
```

Customer buys it.

Later the product price changes:

```text
₹75,000
```

What should the old order show?

Usually:

```text
₹70,000
```

Therefore the order item often stores the price used for that purchase:

```text
unit_price
```

This is an example where copying a value can be intentional because it represents a historical fact, not accidental redundancy.

This is an important lesson:

> Not all duplication is automatically bad.

---

# 84. Sample SQL Design

```sql
CREATE TABLE customers (
    customer_id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(255) NOT NULL UNIQUE
);

CREATE TABLE products (
    product_id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(200) NOT NULL,
    price DECIMAL(10,2) NOT NULL,
    CHECK (price >= 0)
);

CREATE TABLE orders (
    order_id INT PRIMARY KEY AUTO_INCREMENT,
    customer_id INT NOT NULL,
    order_date TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    status VARCHAR(30) NOT NULL DEFAULT 'pending',

    FOREIGN KEY (customer_id)
        REFERENCES customers(customer_id)
);

CREATE TABLE order_items (
    order_id INT NOT NULL,
    product_id INT NOT NULL,
    quantity INT NOT NULL,
    unit_price DECIMAL(10,2) NOT NULL,

    PRIMARY KEY (order_id, product_id),

    FOREIGN KEY (order_id)
        REFERENCES orders(order_id),

    FOREIGN KEY (product_id)
        REFERENCES products(product_id),

    CHECK (quantity > 0),
    CHECK (unit_price >= 0)
);

CREATE TABLE payments (
    payment_id INT PRIMARY KEY AUTO_INCREMENT,
    order_id INT NOT NULL,
    amount DECIMAL(10,2) NOT NULL,
    status VARCHAR(30) NOT NULL,
    paid_at TIMESTAMP NULL,

    FOREIGN KEY (order_id)
        REFERENCES orders(order_id),

    CHECK (amount >= 0)
);
```

---

# 85. Indexing the E-Commerce Database

Possible useful indexes:

```sql
CREATE INDEX idx_orders_customer_id
ON orders(customer_id);
```

Useful for queries such as:

```sql
SELECT *
FROM orders
WHERE customer_id = 5;
```

Another:

```sql
CREATE INDEX idx_order_items_product_id
ON order_items(product_id);
```

Useful for queries such as:

```sql
SELECT *
FROM order_items
WHERE product_id = 10;
```

You should verify indexes with real queries and `EXPLAIN`.

---

# 86. Database Design Workflow

When designing a database from scratch, follow a process.

## Step 1 — Understand the Requirements

Read the application requirements carefully.

Example:

> Customers can place orders.
> Orders contain products.
> A customer may place many orders.
> An order can contain many products.

Extract the nouns and verbs.

Nouns:

```text
Customer
Order
Product
```

Verbs:

```text
places
contains
```

---

## Step 2 — Identify Entities

Turn meaningful concepts into entities:

```text
Customer
Order
Product
```

Then ask whether some relationships need their own entity.

For:

```text
Order <-> Product
```

we need:

```text
OrderItem
```

---

## Step 3 — Identify Attributes

For each entity, list the information you must store.

Customer:

```text
customer_id
name
email
phone
```

Product:

```text
product_id
name
price
```

Order:

```text
order_id
customer_id
order_date
status
```

---

## Step 4 — Choose Primary Keys

Choose a stable unique identifier.

Example:

```text
customers.customer_id
products.product_id
orders.order_id
```

---

## Step 5 — Identify Relationships

Example:

```text
Customer 1 ---- N Order
Order    1 ---- N OrderItem
Product  1 ---- N OrderItem
```

---

## Step 6 — Add Foreign Keys

Example:

```text
orders.customer_id
order_items.order_id
order_items.product_id
```

---

## Step 7 — Apply Constraints

Think about business rules:

```text
email must be unique
name must exist
price cannot be negative
quantity must be positive
```

Convert appropriate rules into database constraints.

---

## Step 8 — Normalize

Check for:

- Repeated data
- Repeating groups
- Partial dependencies
- Transitive dependencies
- Data anomalies

Aim for a clean logical structure.

---

## Step 9 — Think About Queries

Ask:

> What queries will the application run frequently?

Example:

```sql
SELECT *
FROM orders
WHERE customer_id = ?;
```

This can influence index design.

---

## Step 10 — Add Indexes Based on Workload

Don't blindly index everything.

Use:

```sql
EXPLAIN
```

and actual workload measurements.

---

## Step 11 — Think About Transactions

Ask:

> Which operations must succeed or fail together?

Examples:

```text
Create order + order items + payment
Transfer money
Reserve inventory
Update multiple related records
```

These often involve transactions.

---

# 87. Common Database Design Mistakes

## Mistake 1 — One Giant Table

Bad:

```text
customer_name
customer_email
order_id
product_name
product_price
payment_status
...
```

This often causes duplication and anomalies.

---

## Mistake 2 — Storing Lists in One Column

Bad:

```text
skills = "Java, SQL, Python"
```

This can make searching and relationships awkward.

Usually prefer a normalized relationship such as:

```text
students
skills
student_skills
```

when the data represents a true many-to-many relationship.

---

## Mistake 3 — No Primary Key

Every important table should have a clear identity.

Usually:

```sql
PRIMARY KEY
```

---

## Mistake 4 — Using Text Instead of Relationships

Bad:

```text
department_name = 'Engineering'
```

in every employee row when departments have their own identity and data.

Better:

```text
employees.department_id
```

references:

```text
departments.department_id
```

---

## Mistake 5 — Foreign Keys Missing

If two tables logically depend on each other but there is no foreign key, invalid relationships can be inserted unless another mechanism prevents them.

---

## Mistake 6 — Too Many Indexes

Indexes are not free.

Every additional index can create write and storage overhead.

---

## Mistake 7 — Putting Everything in the Application Layer

Do not assume:

> "My Java application validates it, so the database doesn't need constraints."

The database should protect important data integrity rules too.

---

## Mistake 8 — Overusing CASCADE

Cascading deletes can remove large amounts of related data.

Understand the deletion graph before using:

```sql
ON DELETE CASCADE
```

---

## Mistake 9 — Treating Normalization as Absolute

Sometimes controlled denormalization is useful for performance or reporting.

But first understand the normalized design.

A good default is:

> Normalize for correctness first. Denormalize deliberately when evidence justifies it.

---

# 88. Surrogate Key + Business Constraint Pattern

A very common design is:

```sql
CREATE TABLE users (
    user_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    email VARCHAR(255) NOT NULL UNIQUE,
    username VARCHAR(100) NOT NULL UNIQUE
);
```

Here:

```text
user_id
    -> internal identity

email
    -> business uniqueness rule

username
    -> business uniqueness rule
```

This pattern is common because internal identifiers and business attributes have different responsibilities.

---

# 89. INT vs BIGINT for IDs

A database designer must also consider the range of identifiers.

Common options:

```text
INT
BIGINT
```

`BIGINT` supports a much larger integer range than `INT`.

Use a type appropriate for expected scale and your DBMS.

Example:

```sql
user_id BIGINT PRIMARY KEY AUTO_INCREMENT
```

can be a sensible choice for systems expecting very large row counts.

Do not automatically use the largest type for every column without considering storage and indexing costs.

---

# 90. NULL vs NOT NULL

`NULL` means:

> The value is unknown, missing, or not applicable.

It does not simply mean:

```text
0
```

or:

```text
''
```

These are different concepts.

For example:

```text
phone = NULL
```

may mean:

> No phone number is currently stored.

Whereas:

```text
phone = ''
```

means an empty string was stored.

Use `NULL` deliberately.

---

# 91. Important NULL Behavior

This query:

```sql
SELECT *
FROM users
WHERE email = NULL;
```

does not correctly test for NULL.

Use:

```sql
SELECT *
FROM users
WHERE email IS NULL;
```

And:

```sql
WHERE email IS NOT NULL
```

This is important because SQL uses three-valued logic involving:

```text
TRUE
FALSE
UNKNOWN
```

when NULL is involved.

---

# 92. Designing a Good Table

A good table usually answers:

> "What single kind of thing does each row represent?"

Examples:

```text
customers
Each row = one customer

products
Each row = one product

orders
Each row = one order

order_items
Each row = one product line within one order
```

When a table starts representing several unrelated things at once, that is often a warning sign.

---

# 93. Functional Dependency — Beginner Explanation

Functional dependency is useful for understanding normalization.

Suppose:

```text
student_id -> student_name
```

This means:

> If you know the student_id, you can determine the student_name.

Similarly:

```text
course_id -> course_name
```

This helps explain why:

```text
student_name
```

belongs with the student entity and:

```text
course_name
```

belongs with the course entity.

You do not need advanced relational theory to start designing databases, but functional dependencies become very useful when studying normalization deeply.

---

# 94. Entity vs Attribute vs Relationship

This distinction is worth memorizing.

### Entity

A thing/concept with its own identity.

```text
Student
Course
Order
Customer
```

### Attribute

A property of an entity.

```text
Student:
name
email
date_of_birth
```

### Relationship

A connection between entities.

```text
Student enrolls in Course
Customer places Order
```

---

# 95. A Mental Model for Database Design

When you see a new project, think in this order:

```text
REAL WORLD
    |
    v
ENTITIES
    |
    v
ATTRIBUTES
    |
    v
RELATIONSHIPS
    |
    v
KEYS
    |
    v
TABLES
    |
    v
CONSTRAINTS
    |
    v
NORMALIZATION
    |
    v
INDEXES
    |
    v
TRANSACTIONS
```

This is not a rigid law, but it is a useful design workflow.

---

# 96. A Practical Example — College Database

Suppose the requirements are:

> A university has departments.
> Each department has many students.
> Students can enroll in many courses.
> Each course can have many students.
> Each student has one email.
> Email must be unique.

Identify entities:

```text
Department
Student
Course
Enrollment
```

Relationships:

```text
Department 1 ---- N Student
Student    N ---- M Course
```

Resolve the many-to-many relationship:

```text
Student 1 ---- N Enrollment N ---- 1 Course
```

Keys:

```text
department_id
student_id
course_id
(student_id, course_id)
```

Constraint:

```text
student.email UNIQUE
```

Possible tables:

```text
departments
-----------
department_id PK
name

students
--------
student_id PK
name
email UNIQUE
department_id FK

courses
-------
course_id PK
name

enrollments
-----------
student_id PK, FK
course_id PK, FK
```

---

# 97. Questions to Ask Before Creating a Table

Before creating a table, ask:

1. What does one row represent?
2. What is the primary key?
3. Which values must be unique?
4. Which values can be NULL?
5. Which tables does this table depend on?
6. Which tables depend on this table?
7. Is this relationship one-to-one, one-to-many, or many-to-many?
8. Am I unnecessarily duplicating data?
9. What constraints should the database enforce?
10. What queries will use this data?
11. Do those queries need indexes?
12. Which changes need to happen inside a transaction?

---

# 98. Design Checklist

Use this checklist whenever you design a database.

## Modeling

- [ ] Identify entities
- [ ] Identify attributes
- [ ] Identify relationships
- [ ] Determine cardinality
- [ ] Draw an ER diagram

## Keys

- [ ] Choose a primary key for each table
- [ ] Identify foreign keys
- [ ] Identify composite keys where needed
- [ ] Add unique constraints for business uniqueness

## Structure

- [ ] Avoid repeating groups
- [ ] Avoid unnecessary duplication
- [ ] Keep facts in the correct table
- [ ] Review 1NF
- [ ] Review 2NF
- [ ] Review 3NF

## Constraints

- [ ] PRIMARY KEY
- [ ] FOREIGN KEY
- [ ] NOT NULL
- [ ] UNIQUE
- [ ] CHECK
- [ ] DEFAULT

## Indexes

- [ ] Identify frequent WHERE conditions
- [ ] Identify important JOIN columns
- [ ] Consider ORDER BY/GROUP BY patterns
- [ ] Consider composite indexes
- [ ] Use EXPLAIN
- [ ] Remove unnecessary indexes

## Transactions

- [ ] Identify operations that must succeed together
- [ ] Use COMMIT appropriately
- [ ] Use ROLLBACK on failure
- [ ] Understand isolation needs
- [ ] Consider concurrency effects

---

# 99. The Most Important Rules to Remember

If you remember only a few things from this chapter, remember these:

### Rule 1

**A table represents a type of thing.**

```text
customers -> customers
products  -> products
orders    -> orders
```

### Rule 2

**Every important table should have a primary key.**

```sql
PRIMARY KEY
```

### Rule 3

**Foreign keys represent relationships.**

```text
orders.customer_id
        |
        v
customers.customer_id
```

### Rule 4

**The foreign key is normally on the many side of a one-to-many relationship.**

```text
Customer 1 ---- N Order
                  ^
                  |
                  FK
```

### Rule 5

**Many-to-many relationships normally need a junction table.**

```text
Student
Course
Enrollment
```

### Rule 6

**UNIQUE is not the same thing as PRIMARY KEY.**

```text
PRIMARY KEY -> row identity
UNIQUE      -> uniqueness rule
```

### Rule 7

**Normalize to avoid unnecessary duplication and anomalies.**

### Rule 8

**Indexes improve reads but have write/storage costs.**

### Rule 9

**Constraints protect data integrity.**

### Rule 10

**Transactions keep related changes together.**

### Rule 11

**ACID = Atomicity, Consistency, Isolation, Durability.**

---

# 100. Final Mental Picture

A relational database can be understood as:

```text
                         DATABASE
                            |
          +-----------------+-----------------+
          |                 |                 |
       ENTITIES          RELATIONSHIPS      RULES
          |                 |                 |
       TABLES            FOREIGN KEYS      CONSTRAINTS
          |                 |
       COLUMNS             |
          |                 |
        KEYS --------------+
          |
     PRIMARY / UNIQUE

Then:

NORMALIZATION
    |
    v
Reduce unnecessary duplication

INDEXES
    |
    v
Improve data retrieval

TRANSACTIONS
    |
    v
Group related operations

ACID
    |
    v
Make transactions reliable
```

The overall goal of database design is not simply to "make tables."

It is to create a system where:

```text
Data is organized
      +
Relationships are correct
      +
Rules are enforced
      +
Duplication is controlled
      +
Queries are efficient
      +
Changes are reliable
```

That is the foundation of good relational database design.
