# Week 37 — Exercises & Project Task

> [!IMPORTANT]
> ***How to Complete These Exercises***
> Write your answers directly in the highlighted **Your Answer** / **Your SQL** fields below each task. Replace the placeholder text with your own work before submitting.

These exercises accompany the Week 37 Theory material. Complete all sections.

---

## Part 1: TrailShop Project Task

### Task 1: Identify Keys

Using the `products`, `categories`, and `customers` tables shown in Section 2 of this week's Theory material, answer:

1. What is the primary key of the `products` table? Why is it a good choice?

> [!NOTE]
> ***Your Answer***
>
> *Primary key of products: product_id. It is a surrogate key, making it an ideal choice because it is single-column, unique, immutable (never changes), has no business meaning, and is never NULL.*
>
>
>
>


2. What is the primary key of the `categories` table?

> [!NOTE]
> ***Your Answer***
>
> *Primary key of categories: category_id*
>
>
>
>


3. What is the foreign key in the `products` table? What does it reference?

> [!NOTE]
> ***Your Answer***
>
> *category_id. It references categories(category_id).*
>
>
>
>


4. Is `name` in `products` a candidate key? Under what assumption? What would make it unsuitable as a primary key?


> [!NOTE]
> ***Your Answer***
>
> *Yes, under the assumption that no two products share the exact same name. However, it is unsuitable as a primary key because product names can change or be long text strings, making them inefficient for joins and unstable over time.*
>
>
>
>
5. Give an example of a **superkey** for the `products` table that is NOT a candidate key. Explain why it's not minimal.

> [!NOTE]
> ***Your Answer***
>
> *{product_id, name}. its a superkey because together they identify a product, but its not minimal because you dont need the name part—product_id by itself is already enough.*
>
>
>
>

6. Give an example of a **composite key** using a hypothetical `order_items` table. Explain why neither column alone would be sufficient.

> [!NOTE]
> ***Your Answer***
>
> *(order_id, product_id). neither column can be the key by itself because an order can have many products, and a product can be in many orders. you need both together to find a specific order item.*
>
>
>
>

7. Is `email` in `customers` a candidate key? What makes it different from `customer_id` as a PK choice? *(See Section 6.9 on natural vs surrogate keys.)*

> [!NOTE]
> ***Your Answer***
>
> *yes, because every customer has a unique email. the difference is that email is a natural key (real info that can change), while customer_id is a surrogate key (just a random number created by the system that never changes).*
>
>
>
>

### Task 2: Define Business Rules

List **5 business rules** for TrailShop. For each rule, specify:
- The rule in plain English
- Which constraint type(s) would enforce it
- Which table and column the constraint applies to
- The SQL syntax for the constraint

Example:

| Business Rule | Constraint Type | Table.Column | SQL |
|---|---|---|---|
| Every product must have a price greater than zero | CHECK | products.price | `CHECK (price > 0)` |
| ... | ... | ... | ... |

Think about rules for customers, orders, and categories — not just products.

> [!NOTE]
> ***Your Answer***
>
> *| Business Rule                                 | Constraint Type | Table.Column             | SQL
| Customer emails must be unique                | UNIQUE          | customers.email          | email VARCHAR(255) UNIQUE                                          |
| Order status must be one of the allowed types | CHECK           | orders.status            | CHECK (status IN ('pending', 'shipped', 'delivered', 'cancelled')) |
| Product stock cannot be negative              | CHECK           | products.stock_quantity  | CHECK (stock_quantity >= 0)                                        |
| Every order needs a real customer             | FOREIGN KEY     | orders.customer_id       | FOREIGN KEY (customer_id) REFERENCES customers(customer_id)        |
| Category names must be unique                 | UNIQUE          | categories.category_name | category_name VARCHAR(50) UNIQUE                                   |
*
>
>
>
>

### Task 3: Integrity Violations

For each SQL statement below, predict whether it will **succeed** or **fail**. If it fails, explain which integrity rule or constraint is violated and what error message you'd expect. Assume the schema from Section 9.8 of the Theory material.

```sql
-- Statement A
INSERT INTO categories (category_id, category_name)
VALUES (NULL, 'Cycling');

-- Statement B
INSERT INTO products (product_id, name, price, stock_quantity, category_id)
VALUES (109, 'AeroLite Tent', 279.00, 10, 2);

-- Statement C
INSERT INTO products (product_id, name, price, stock_quantity, category_id)
VALUES (110, 'BudgetBoots', -5.00, 25, 1);

-- Statement D
INSERT INTO products (product_id, name, price, stock_quantity, category_id)
VALUES (103, 'Duplicate Shoes', 99.99, 5, 3);

-- Statement E
INSERT INTO products (product_id, name, price, stock_quantity, category_id)
VALUES (111, 'CloudWalker Sandals', 65.00, 40, 10);

-- Statement F
INSERT INTO products (product_id, name, price, stock_quantity, category_id)
VALUES (112, NULL, 89.99, 20, 1);

-- Statement G
INSERT INTO products (product_id, name, price, stock_quantity, category_id)
VALUES (113, 'LightStep Shoes', 149.00, -3, 1);

-- Statement H
INSERT INTO order_items (order_id, product_id, quantity, unit_price)
VALUES (1001, 101, 0, 189.50);
```

> [!NOTE]
> ***Your Answer***
>
> *statement a: FAIL. you cant put NULL into category_id because it is part of the primary key (entity integrity violation).
> statement b: SUCCESS. everything is correct and category 2 actually exists.
> statement c: FAIL. price cant be negative (CHECK (price > 0) violation).
> statement d: FAIL. product id 103 already exists (primary key duplicate error).
> statement e: FAIL. category 10 does not exist in the categories table (foreign key violation).
> statement f: FAIL. product name cannot be NULL (not-null violation).
> statement g: FAIL. stock quantity cant be negative (CHECK violation).
> statement h: FAIL. order quantity cant be zero (CHECK (quantity > 0) violation).*
>
>

### Task 4: Foreign Key Actions

Consider the following scenario using the schema from Theory Section 9.8:

1. You want to delete category 2 ("Camping") from the `categories` table. Products 102 and 106 reference this category. What happens with:
   - `ON DELETE RESTRICT`?
   - `ON DELETE CASCADE`?
   - `ON DELETE SET NULL`? (Assume `category_id` in `products` allows NULL for this question)

2. Which foreign key action would you recommend for the TrailShop `products.category_id` → `categories.category_id` relationship? Justify your choice in 2–3 sentences.

> [!NOTE]
> ***Your Answer***
>
> ON DELETE RESTRICT: stops the delete right away and gives an error because products are still using that category.
> ON DELETE CASCADE: deletes category 2 and also automatically deletes all the products inside that category.
> ON DELETE SET NULL: deletes category 2 and changes the category id of those products to NULL (uncategorized).
> recommendation: use RESTRICT. you dont want to accidentally delete all your products just because you deleted a category, and you dont want them to lose their category by accident either.*
>
>
>
>




---

## Part 2: Theory Review Questions

Answer each question in 2–4 sentences unless otherwise specified. Reference the Theory material sections as needed.

### Short-Answer Questions

**Q1.** Define the following terms in your own words: relation, tuple, attribute, domain. Give one TrailShop example for each.

> [!NOTE]
> ***Your Answer***
>
> *a relation is just a table with rows and columns (like products). a tuple is a single row (like one product). an attribute is a column (like price). a domain is the allowed list of values a column can take (like only positive numbers for price)*
>
>
>
>

*(See Sections 2 and 3 of this week's Theory material.)*

**Q2.** What makes a candidate key different from a primary key? Can a table have more than one candidate key?


> [!NOTE]
> ***Your Answer***
>
> *a candidate key is any group of columns that can uniquely identify a row. a primary key is just the one candidate key you actually pick to use. yes, a table can have more than one candidate key.*
>
>
>
>

*(See Section 6 of this week's Theory material.)*

**Q3.** Explain entity integrity in your own words. Why can't a primary key be NULL?


> [!NOTE]
> ***Your Answer***
>
> *entity integrity means every table must have a primary key and it cant be null. it cant be null because the database needs to know which exact row it is looking at.*
>
>
>
>

*(See Section 8.1 of this week's Theory material.)*

**Q4.** What happens when referential integrity is violated? Give a concrete TrailShop example — show the SQL statement and the expected error.

> [!NOTE]
> ***Your Answer***
>
> *when referential integrity fails, the database rejects what you are trying to do. example: trying to add a product with category id 99 when category 99 doesnt exist. the database will say key is not present in table categories.*
>
>
>
>

*(See Section 8.2 of this week's Theory material.)*

**Q5.** Explain the difference between a surrogate key and a natural key. Give an example of each for a `books` table in a library database.

> [!NOTE]
> ***Your Answer***
>
> *a surrogate key is a fake id number made by the database (like book_id = 5). a natural key is real info from the world (like an ISBN number for a book).*
>
>
>
>

*(See Section 6.8–6.9 of this week's Theory material.)*

**Q6.** What is a NULL value? Why is `WHERE price = NULL` wrong? What should you write instead?


> [!NOTE]
> ***Your Answer***
>
> *null means unknown or missing data. writing WHERE price = NULL is wrong because sql doesnt know how to compare things to null with = symbol. you always have to write WHERE price IS NULL.*
>
>
>
>

*(See Section 7 of this week's Theory material.)*

**Q7.** What is a junction table? When is it needed? Give an example.

> [!NOTE]
> ***Your Answer***
>
> *a junction table is an extra table used to connect two other tables that have a many-to-many relationship (like linking products to tags).*
>
>
>
>

*(See Section 12.3 of this week's Theory material.)*

**Q8.** Describe the three types of relationships (1:1, 1:N, M:N). For each, give one TrailShop example.

> [!NOTE]
> ***Your Answer***
>
> *1:1: one thing connects to one other thing (like products and product details).
> 1:N: one thing connects to many things (like one category having many products).
> M:N: many things connect to many things using a middle table (like products and tags).*
>
>
>
>

*(See Section 12 of this week's Theory material.)*

**Q9.** What is the difference between `ON DELETE CASCADE` and `ON DELETE RESTRICT`? When would you use each?


> [!NOTE]
> ***Your Answer***
>
> *CASCADE deletes everything attached to it automatically when you delete the main thing. RESTRICT blocks the delete so you dont mess things up by accident. use restrict to be safe*
>
>
>
>

*(See Section 10 of this week's Theory material.)*

**Q10.** Explain what "atomic entries" means in the context of relation properties. Give an example of a violation.

> [!NOTE]
> ***Your Answer***
>
> *atomic entries means every cell in the table only holds one single value, not a bunch of things stuffed together in a list. bad example: writing "footwear, hiking" in one single box.*
>
>
>
>

*(See Section 5.3 of this week's Theory material.)*

### True/False

For each statement, write **True** or **False** and correct any false statements.

1. A superkey is always a candidate key.
2. A primary key can consist of more than one column.
3. NULL = NULL evaluates to TRUE in SQL.
4. A foreign key must always be NOT NULL.
5. Referential integrity ensures that every FK value matches an existing PK value (or is NULL).
6. The degree of a relation is the number of rows.

### Matching Exercise

Match each term (1–12) with its definition (A–L).

| # | Term |
|---|---|
| 1 | Superkey |
| 2 | Candidate key |
| 3 | Composite key |
| 4 | Foreign key |
| 5 | Alternate key |
| 6 | Surrogate key |
| 7 | Natural key |
| 8 | Orphan record |
| 9 | Domain |
| 10 | Junction table |
| 11 | Cardinality |
| 12 | COALESCE |

| Letter | Definition |
|---|---|
| A | The set of all permitted values for an attribute |
| B | A key composed of two or more attributes |
| C | A row whose FK references a non-existent PK — forbidden by referential integrity |
| D | An artificial key with no business meaning (e.g., auto-generated ID) |
| E | A candidate key not chosen as the primary key |
| F | Any set of attributes that uniquely identifies every tuple |
| G | A minimal superkey — no attribute can be removed without losing uniqueness |
| H | A column that references the primary key of another table |
| I | The number of tuples (rows) in a relation |
| J | A key drawn from real-world data with business meaning |
| K | A table implementing a many-to-many relationship |
| L | A SQL function that returns the first non-NULL argument |


> [!NOTE]
> ***false. superkeys can have extra unneeded columns, but candidate keys must be as small as possible.
> true.
> false. it gives unknown, not true.
> false. foreign keys can sometimes be null if allowed.
> true.
> false. degree is the number of columns, cardinality is the rows.***
>
> | # | Your Match |
> |---|---|
> | 1 |f |
> | 2 |g |
> | 3 |b |
> | 4 |h |
> | 5 |e |
> | 6 |d |
> | 7 |j |
> | 8 |c |
> | 9 |a |
> | 10 |k |
> | 11 | i|
> | 12 | l|
>

---

## Part 3: SQL Practice — Constraints in Action

These exercises test your understanding of constraints. You do NOT need to run these in PostgreSQL (but you may if you'd like to verify your answers).

### Exercise 3.1: Predict the Outcome

Given the following table definitions:

```sql
CREATE TABLE departments (
    dept_id   INTEGER      PRIMARY KEY,
    dept_name VARCHAR(50)  NOT NULL UNIQUE
);

CREATE TABLE employees (
    emp_id    INTEGER       PRIMARY KEY,
    name      VARCHAR(100)  NOT NULL,
    salary    NUMERIC(10,2) NOT NULL CHECK (salary >= 0),
    dept_id   INTEGER       NOT NULL REFERENCES departments(dept_id)
);
```

Assume these rows already exist:

```sql
INSERT INTO departments VALUES (1, 'Engineering');
INSERT INTO departments VALUES (2, 'Marketing');
INSERT INTO employees VALUES (100, 'Alice', 75000, 1);
INSERT INTO employees VALUES (101, 'Bob', 65000, 2);
```

For each statement below, predict: **SUCCESS** or **FAIL**? If fail, name the violated constraint.

```sql
-- 1
INSERT INTO employees VALUES (102, 'Carol', 70000, 1);

-- 2
INSERT INTO employees VALUES (103, 'Dan', -5000, 1);

-- 3
INSERT INTO employees VALUES (100, 'Eve', 80000, 2);

-- 4
INSERT INTO employees VALUES (104, 'Frank', 60000, 5);

-- 5
INSERT INTO departments VALUES (3, 'Engineering');

-- 6
INSERT INTO employees VALUES (105, NULL, 55000, 2);

-- 7
DELETE FROM departments WHERE dept_id = 1;

-- 8
INSERT INTO employees VALUES (106, 'Grace', 0, 2);
```

### Exercise 3.2: Write the Constraints

Given these business rules for a **bookstore database**, write the `CREATE TABLE` statements with appropriate constraints:

1. Every book has a unique ISBN (13 characters), a title (required), a price (must be positive), and a publication year.
2. Every author has an ID, a first name (required), and a last name (required).
3. A book can have multiple authors, and an author can write multiple books.
4. Every book belongs to exactly one genre. Genres have an ID and a unique name.
5. Publication year must be between 1450 and the current year.

*(Hint: you'll need at least 4 tables, including a junction table for the M:N relationship.)*
my answer 
*CREATE TABLE genres (
    genre_id INTEGER PRIMARY KEY,
    genre_name VARCHAR(50) NOT NULL UNIQUE
);

CREATE TABLE books (
    isbn VARCHAR(13) PRIMARY KEY,
    title VARCHAR(150) NOT NULL,
    price NUMERIC(10,2) NOT NULL CHECK (price > 0),
    publication_year INTEGER NOT NULL CHECK (publication_year BETWEEN 1450 AND 2026),
    genre_id INTEGER NOT NULL REFERENCES genres(genre_id)
);

CREATE TABLE authors (
    author_id INTEGER PRIMARY KEY,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL
);

CREATE TABLE book_authors (
    isbn VARCHAR(13) REFERENCES books(isbn) ON DELETE CASCADE,
    author_id INTEGER REFERENCES authors(author_id) ON DELETE CASCADE,
    PRIMARY KEY (isbn, author_id)
);*
---

## Part 4: Design Exercise — Library System

A small public library needs a database. Here is a description of their requirements:

> The library has a collection of **books**. Each book has an ISBN, a title, a publication year, and belongs to one genre (Fiction, Non-Fiction, Science, History, etc.). The library may own multiple **copies** of the same book — each copy has a unique barcode sticker.
>
> The library has registered **members**. Each member has a member number, name, email, and phone. Members can **borrow** copies. Each borrowing records which member borrowed which copy, the borrow date, the due date, and the return date (NULL if not yet returned).
>
> **Rules:**
> - A member can borrow at most 5 copies at any given time.
> - The due date is always 14 days after the borrow date.
> - A copy cannot be borrowed if it's currently not returned (return_date IS NULL).

### Your Tasks

1. **Identify the tables** you would need (list them with their columns).
2. **Identify the primary key** for each table. Are they surrogate or natural keys? Justify your choices.
3. **Identify all foreign keys** and the tables they reference.
4. **Identify any candidate keys** beyond the primary key (alternate keys).
5. **List the business rules** from the description and map each to a constraint type. Which rules cannot be enforced by simple constraints?


> [!NOTE]
> ***Your Answer***
>
> *tables & columns:
> genres: genre_id, genre_name
> books: isbn, title, publication_year, genre_id
> copies: barcode, isbn
> members: member_id, name, email, phone
> borrowings: borrow_id, member_id, barcode, borrow_date, due_date, return_date
>
>
> primary keys:
> genre_id (surrogate)
> isbn (natural)
> barcode (natural)
> member_id (surrogate)
> borrow_id (surrogate)
>
> 
> foreign keys:
> books.genre_id points to genres.genre_id
> copies.isbn points to books.isbn
> borrowings.member_id points to members.member_id
> borrowings.barcode points to copies.barcode
>
> 
> candidate keys:
> members.email, genres.genre_name
>
> 
> business rules:
> due date is 14 days after borrow date (CHECK constraint can help)
> a member can borrow max 5 books at once (needs code / triggers, not a simple sql constraint)
> a copy cant be borrowed if its not returned yet (also needs triggers)*
>
>
>
>
6. **Write the CREATE TABLE statements** for at least the `books`, `copies`, and `borrowings` tables with full constraints.
my answer
*CREATE TABLE genres (
    genre_id INTEGER PRIMARY KEY,
    genre_name VARCHAR(50) NOT NULL UNIQUE
);

CREATE TABLE books (
    isbn VARCHAR(13) PRIMARY KEY,
    title VARCHAR(200) NOT NULL,
    publication_year INTEGER NOT NULL,
    genre_id INTEGER NOT NULL REFERENCES genres(genre_id)
);

CREATE TABLE copies (
    barcode VARCHAR(50) PRIMARY KEY,
    isbn VARCHAR(13) NOT NULL REFERENCES books(isbn) ON DELETE RESTRICT
);

CREATE TABLE members (
    member_id INTEGER PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(255) NOT NULL UNIQUE,
    phone VARCHAR(30)
);

CREATE TABLE borrowings (
    borrow_id INTEGER GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    member_id INTEGER NOT NULL REFERENCES members(member_id),
    barcode VARCHAR(50) NOT NULL REFERENCES copies(barcode),
    borrow_date DATE NOT NULL DEFAULT CURRENT_DATE,
    due_date DATE NOT NULL,
    return_date DATE,
    CHECK (due_date >= borrow_date),
    CHECK (return_date IS NULL OR return_date >= borrow_date)
);*
---

## Submission Checklist

- [x ] Task 1: Key identification answers (Part 1)
- [ x] Task 2: Business rules table with 5 rules (Part 1)
- [ x] Task 3: Integrity violation predictions with explanations (Part 1)
- [ x] Task 4: Foreign key action analysis (Part 1)
- [ x] Theory Review Questions answered (Part 2)
- [ x] SQL Practice — constraint predictions and bookstore CREATE TABLE (Part 3)
- [ x] Library System design exercise (Part 4)
