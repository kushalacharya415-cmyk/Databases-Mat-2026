# Week 36 — Exercises & Project Task

> [!IMPORTANT]
> ***How to Complete These Exercises***
> Write your answers directly in the highlighted **Your Answer** / **Your SQL** fields below each task. Replace the placeholder text with your own work before submitting.

These exercises accompany the Week 36 Theory material. Complete all sections.

---

## Part 1: TrailShop Project Task

### Task 1: Set Up PostgreSQL

Goal: install a working PostgreSQL server on your computer and confirm you can start `psql` (or an equivalent client).

---

### Local installation

**Step 1 — Download the installer**

1. Open [https://www.postgresql.org/download/windows/](https://www.postgresql.org/download/windows/).
2. Click **Download the installer** (EDB installer is fine).
3. Choose a recent stable version (e.g. 16 or 17) for **Windows x86-64**.
4. Save the `.exe` file and run it.

_(macOS / Linux: start from [https://www.postgresql.org/download/](https://www.postgresql.org/download/) and follow the OS-specific guide.)_

**Step 2 — Run the setup wizard**

Work through the wizard. Suggested choices for this course:

1. **Installation directory** — leave the default.
2. **Select components** — keep at least **PostgreSQL Server**, **pgAdmin 4**, **Command Line Tools**, and **Stack Builder** (Stack Builder can be skipped later).
3. **Data directory** — leave the default.
4. **Password** — set a password for the superuser account `postgres`.
   **Write it down.** You will need it every time you connect.
5. **Port** — leave **5432** (default) unless that port is already in use.
6. **Locale** — leave the default.
7. Finish the install. You can decline launching Stack Builder if prompted.

**Step 3 — Confirm the service is running (Windows)**

1. Press `Win`, type **Services**, open **Services**.
2. Find a service named like `postgresql-x64-16` (version number may differ).
3. Status should be **Running**. If not, right-click → **Start**.

**Step 4 — Open a terminal where** `psql` **is available**

On Windows, the easiest reliable options are:

- **Option 4a:** Start Menu → **SQL Shell (psql)** (installed with PostgreSQL), **or**
- **Option 4b:** Open **PowerShell** or **Command Prompt**.

If `psql` is not found in PowerShell, either use **SQL Shell (psql)** or add the PostgreSQL `bin` folder to your PATH, for example:

```
C:\Program Files\PostgreSQL\16\bin
```

(Adjust `16` to match your installed version.)

**Step 5 — Verify the installation**

In PowerShell / Command Prompt, run:

```
psql --version
```

You should see something like `psql (PostgreSQL) 16.x`.

If you used **SQL Shell (psql)** instead, you can skip `--version` and go straight to connecting in Task 2 — successful connection also proves the install works.

**Step 6 — Capture evidence for submission**

Take a screenshot of `psql --version` output (or of a successful `psql` connection prompt).

---

### Task 2: Create the TrailShop Database

Goal: create an empty database named `trailshop` and confirm you are connected to it.

Complete these steps after Task 1 succeeds.

**Step 1 — Connect to the PostgreSQL server as** `postgres`

**If you use SQL Shell (psql) on Windows**, press Enter to accept defaults for Server, Database, Port, and Username (`postgres`), then type the password you set during install when prompted.

**If you use PowerShell / Command Prompt**, run:

```
psql -U postgres -h localhost -p 5432
```

Enter the `postgres` password when asked.

You should see a prompt similar to:

```
postgres=#
```

That means you are connected to the default `postgres` maintenance database — which is normal before creating `trailshop`.

**Step 2 — List existing databases (optional but useful)**

At the `postgres=#` prompt, run:

```
\l
```

Scan the list. If `trailshop` already exists from an earlier attempt, skip Step 3 and go to Step 4.

**Step 3 — Create the database**

Still at the `postgres=#` prompt, run exactly:

```sql
CREATE DATABASE trailshop;
```

Expected result:

```
CREATE DATABASE
```

If you see `ERROR: database "trailshop" already exists`, the database is already there — continue to Step 4.

**Step 4 — Connect to** `trailshop`

In `psql`, run:

```
\c trailshop
```

Expected result (wording may vary slightly):

```
You are now connected to database "trailshop" as user "postgres".
```

**Step 5 — Confirm the prompt and that the database is empty**

1. Check that your prompt shows `trailshop`, for example:

```
trailshop=#
```

1. List tables:

```
\dt
```

You should see **no tables** (or a message that no relations were found). That is expected in Week 36 — tables come later.

1. Confirm the current database name with SQL:

```sql
SELECT current_database();
```

Expected: one row with `trailshop`.

**Step 6 — Quit psql (when finished)**

```
\q
```

**Step 7 — Capture evidence for submission**

Take a screenshot showing:

- successful `\c trailshop` (or the `trailshop=#` prompt), **and**
- `\dt` with an empty result / “Did not find any relations”

---

### Troubleshooting (Tasks 1–2)

| Problem                                | What to try                                                                                                                  |
| -------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| `psql` is not recognized               | Use **SQL Shell (psql)**, or add PostgreSQL’s `bin` folder to PATH and open a **new** terminal                               |
| Password authentication failed         | Use the password set for user `postgres` during install; check Caps Lock                                                     |
| Connection refused / could not connect | Confirm the PostgreSQL Windows service is **Running**; confirm port **5432**                                                 |
| Port already in use                    | Either stop the other service using 5432, or reinstall/reconfigure PostgreSQL to another port and always pass `-p` that port |
| Permission denied to create database   | Connect as `postgres` (superuser), not as a limited role                                                                     |

---

### Task 3: Reflection Worksheet

Answer the following in your own words (write 2–3 sentences per point):

1. List **3 specific problems** TrailShop would face if they kept using spreadsheets as their product catalog grows to 5,000+ items with 10 staff members.

> [!NOTE]
> ***Your Answer***
>
> _the three specfic probles in this case are
> concurrency issues: when many staff try to edit the inventory or prices ar the same time, some changes will overwrite others people work, and data get lost
>
> Data Redundancy: Product info is scattered in many files, so a sale price might update on website but stay high in inventory sheet, which confuses customers.
> Lack of Integrity: Spreadsheets do not have strict rules, so someone can easily type bad data like negative prices or missing names without any error._

2. List **3 benefits** of switching to a database system, explaining how each one solves a problem from your list above.

> [!NOTE]
> ***Your Answer***
>
> _the three benefits are
> Concurrency Control: PostgreSQL manages multiple users at same time safely, so staff can work together without deleting each other changes.
> Single Source of Truth: Storing each price or product fact in only one place stops all the mismatched prices between different department sheets.
>automated constraints: Setting rules like price must be bigger then zero means the database will instantly reject wrong data entries. _

3. Explain the three-schema architecture in your own words. Why is the separation into three levels useful?

> [!NOTE]
> ***Your Answer***
>
> _The three schema architecture splits database into three levels: External level (custom views for different teams like marketing), Conceptual level (the full logical structure), and Internal level (how data is actually saved on hard drive). This is useful because it gives data independence, meaning you can change physical storage or add columns without breaking the user apps._

---

## Part 2: Theory Review Questions

Answer each question in 2–4 sentences. Reference the Theory material sections as needed.

### Short-Answer Questions

**Q1.** What is the difference between data and information? Give a concrete example using TrailShop data.
_(See Section 1 of this week's Theory material.)_

> [!NOTE]
> ***Your Answer***
>
> _Data is just raw and unprocessed facts with no context, but information is data that is organized so it actually makes sense. For example, ("TrailMaster X4", 149.99, 12) is just raw data, but saying "The product TrailMaster X4 costs 149.99 euros and we have 12 in stock" is useful information._

**Q2.** List and explain three disadvantages of file-based data management systems. For each, describe how it would affect TrailShop specifically.
_(See Section 2 of this week's Theory material.)_

> [!NOTE]
> ***Your Answer***
>
> _The bad things are data redundancy (which creates conflicting prices between inventory and web sheets), data isolation (makes it hard to match customer files with order texts), and program-data dependence (where changing a CSV column breaks all the custom scripts)._

**Q3.** What is a DBMS? List four of its core functions.
_(See Sections 3 and 4 of this week's Theory material.)_

> [!NOTE]
> ***Your Answer***
>
> _A DBMS is software that sits between programs and stored data to help manage it. Four main functions are Data Definition (DDL), Data Manipulation (DML), Concurrency Control, and Backup and Recovery._

**Q4.** Explain program-data independence with a concrete example. Why is it important?
_(See Section 5.2 of this week's Theory material.)_

> [!NOTE]
> ***Your Answer***
>
> _Program-data independence means you can change how database is structured without breaking the apps that use it. For example, if you add a weight column to products table, old website queries still work fine. It is important because it saves time and stops code from breaking when database changes._

**Q5.** What is metadata? Give two examples of metadata for a `products` table.
_(See Section 8 of this week's Theory material.)_

> [!NOTE]
> ***Your Answer***
>
> _Metadata is basically data about data that describes how the database is built. Two examples for products table are the table name itself and the data types of its columns like price being numeric._

**Q6.** What is the three-schema architecture? Name and briefly describe each level.
_(See Section 3.3 of this week's Theory material.)_

> [!NOTE]
> ***Your Answer***
>
> _It is a framework that separates database into three layers: External level (user views), Conceptual level (the whole logical design), and Internal level (how data is physically stored on disk)._

**Q7.** Explain the difference between logical data independence and physical data independence.
_(See Section 3.4 of this week's Theory material.)_

> [!NOTE]
> ***Your Answer***
>
> _Logical independence let you change the conceptual schema like adding new columns without breaking user views. Physical independence let you change how data is stored on disk like moving to a faster drive without changing the logical schema._

**Q8.** What is a transaction? Why is atomicity important? Give a TrailShop example.
_(See Section 5.5 of this week's Theory material.)_

> [!NOTE]
> ***Your Answer***
>
> _A transaction is a group of steps that must all finish together or nothing happens. Atomicity is important because if something fails halfway, it rolls back everything. For TrailShop, when someone buys an item, updating the order and lowering the stock must happen together so stock does not drop if payment fails._

### True/False

For each statement, write **True** or **False** and correct any false statements.

1. A DBMS stores only data, not information about the data's structure.
2. In a file-based system, changing the format of a data file requires updating every program that reads it.
3. Data redundancy means the same data is stored in multiple places.
4. PostgreSQL is a commercial, closed-source database system.
5. The conceptual level of the three-schema architecture describes how data is physically stored on disk.

> [!NOTE]
> ***Your Answer***
>
> _False. A DBMS stores both data and metadata about its structure.
> True.
> True.
> False: PostgreSQL is a free and open-source database system.
> False. The conceptual level describes the logical structure, while the internal level describes how data is stored physically on disk._

### Matching Exercise

Match each term (1–10) with its definition (A–J).

| #   | Term              |
| --- | ----------------- |
| 1   | Data dictionary   |
| 2   | RDBMS             |
| 3   | Concurrency       |
| 4   | View              |
| 5   | Schema            |
| 6   | Data isolation    |
| 7   | Transaction       |
| 8   | SQL               |
| 9   | Data independence |
| 10  | ACID              |

| Letter | Definition                                                                          |
| ------ | ----------------------------------------------------------------------------------- |
| A      | A virtual table defined by a query, showing a subset of data                        |
| B      | Multiple users accessing data at the same time                                      |
| C      | The formal definition of a database's structure (tables, columns, types)            |
| D      | A logical unit of work that must complete fully or not at all                       |
| E      | The standard language for querying and managing relational databases                |
| F      | The system catalog storing metadata about the database                              |
| G      | Data trapped in separate files/formats that are hard to combine                     |
| H      | A DBMS based on the relational model, using tables and SQL                          |
| I      | The ability to change storage or structure without affecting applications           |
| J      | Atomicity, Consistency, Isolation, Durability — properties of reliable transactions |

> [!NOTE]
> ***Your Answers***
>
> | #   | Your Match |
> | --- | ---------- |
> | 1   |    f        |
> | 2   |    h        |
> | 3   |    b        |
> | 4   |    a        |
> | 5   |    c        |
> | 6   |    g        |
> | 7   |    d        |
> | 8   |    e        |
> | 9   |    i        |
> | 10  |    j        |

---

## Part 3: Practical Exercises

These exercises require a working PostgreSQL installation. See Part 1, Task 1 if you haven't set it up yet.

### Exercise 3.1: Explore psql

Connect to PostgreSQL using psql and complete the following. Write down the command you used and the output (or a summary of it).

1. List all databases on your server.
2. Connect to the `trailshop` database.
3. List all tables in the `trailshop` database.
4. Use `\?` to display the list of psql meta-commands. Find and write down the commands for:

- Describing a specific table's structure
- Listing all users/roles
- Showing help for a specific SQL command

5. Quit psql.

> [!NOTE]
> ***Your Answer***
>
> _1. List the databases command: \l
> 2. Connect to the database command: \c trialshop
> 3. List tables: commands: \dt
> 4. FInd psql meta commands
> describing a table \d tablename
> listing user/roles: \du
> showing help for an SQL command \h SQL_COMMAND
> 5. QUIT psql command : \q_

### Exercise 3.2: Explore the System Catalog

While connected to `trailshop`, run the following queries and write down what they return:

```sql
SELECT current_database();
```

```sql
SELECT version();
```

```sql
SELECT table_name FROM information_schema.tables
WHERE table_schema = 'public';
```

Why does the last query return no rows? What would you expect to see after creating tables in future weeks?

> [!NOTE]
> ***Your Answer***
>
> _It returns nothing because we have not created any tables yet for Week 36. Later in next weeks when we create tables, they will show up here._

### Exercise 3.3: Create and Drop a Test Database

Practice database creation and deletion:

```sql
-- Create a test database
CREATE DATABASE test_playground;

-- List databases to confirm it exists
\l

-- Drop (delete) the test database
DROP DATABASE test_playground;

-- List databases again to confirm it's gone
\l
```

**Warning:** `DROP DATABASE` permanently deletes a database and all its data. Always double-check the database name before running this command.

---

## Submission Checklist

- [ ] PostgreSQL installed and working (screenshot of `psql --version` or equivalent)
- [ ] `trailshop` database created (screenshot of `\c trailshop` showing successful connection)
- [ ] Reflection Worksheet answers (Part 1, Task 3)
- [ ] Theory Review Questions answered (Part 2)
- [ ] Practical Exercise outputs documented (Part 3)
