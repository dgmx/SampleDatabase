
### MySQL's Sample Employee Database

Reference: MySQL's Sample Employees Database @ [http://dev.mysql.com/doc/employee/en/index.html](http://dev.mysql.com/doc/employee/en/index.html).

This is a rather simple database with 6 tables but with millions of records.

#### Database and Tables

There are 6 tables as follows:

![Database diagram](images/SampleEmployees.png)

##### Table "employees"

    CREATE TABLE **employees** (
    emp\_no      INT             NOT NULL,  \-- UNSIGNED AUTO\_INCREMENT??
    birth\_date  DATE            NOT NULL,
    first\_name  VARCHAR(14)     NOT NULL,
    last\_name   VARCHAR(16)     NOT NULL,
    gender      ENUM ('M','F')  NOT NULL,  \-- Enumeration of either 'M' or 'F'  
    hire\_date   DATE            NOT NULL,
    PRIMARY KEY (emp\_no)                   \-- Index built automatically on primary-key column
                                           \-- INDEX (first\_name)
                                           -- INDEX (last\_name)
    );

There are 300,024 records for this table.

##### Table "departments"

    CREATE TABLE **departments** (
    dept\_no     CHAR(4)         NOT NULL,  \-- in the form of 'dxxx'
    dept\_name   VARCHAR(40)     NOT NULL,
    PRIMARY KEY (dept\_no),                 \-- Index built automatically
    UNIQUE  KEY (dept\_name)                \-- Build INDEX on this unique-value column
    );

The keyword `KEY` is synonym to `INDEX`. An `INDEX` can be built on unique-value column (`UNIQUE KEY` or `UNIQUE INDEX`) or non-unique-value column (`KEY` or `INDEX`). Indexes greatly facilitates fast search. However, they deplete the performance in `INSERT`, `UPDATE` and `DELETE`. Generally, relational databases are optimized for retrievals, and NOT for modifications.

There are 9 records for this table.

##### Table "dept\_emp"

Junction table to support between many-to-many relationship between `employees` and `departments`. A department has many employees. An employee can belong to different department at different dates, and possibly concurrently.

    CREATE TABLE **dept\_emp** (
    emp\_no      INT         NOT NULL,
    dept\_no     CHAR(4)     NOT NULL,
    from\_date   DATE        NOT NULL,
    to\_date     DATE        NOT NULL,
    KEY         (emp\_no),   \-- Build INDEX on this non-unique-value column
    KEY         (dept\_no),  \-- Build INDEX on this non-unique-value column
    FOREIGN KEY (emp\_no) REFERENCES employees (emp\_no) ON DELETE CASCADE,
           \-- Cascade DELETE from parent table 'employee' to this child table
           -- If an emp\_no is deleted from parent 'employee', all records
           --  involving this emp\_no in this child table are also deleted
           -- ON UPDATE CASCADE??
    FOREIGN KEY (dept\_no) REFERENCES departments (dept\_no) ON DELETE CASCADE,
           \-- ON UPDATE CASCADE??
    PRIMARY KEY (emp\_no, dept\_no)
           \-- Might not be unique?? Need to include from\_date
    );

The foreign keys have `ON DELETE` _reference action_ of `CASCADE`. If a record having a particular key-value from the parent table (employees and departments) is deleted, all the records in this child table having the same key-value are also deleted. Take note that the default `ON DELETE` reference action of is `RESTRICTED`, which disallows `DELETE` on the parent record, if there are matching records in the child table.

There are two reference actions: `ON DELETE` and `ON UPDATE`. The `ON UPDATE` reference action of is defaulted to `RESTRICT` (or disallow). It is more meaningful to set `ON UPDATE` to `CASCADE`, so that changes in parent table (e.g., change in `emp_no` and `dept_no`) can be cascaded down to the child table(s).

There are 331,603 records for this table.

##### Table "dept\_manager"

join table to support between many-to-many relationship between `employees` and `departments`. Same structure as `dept_emp`.

    CREATE TABLE **dept\_manager** (
    dept\_no      CHAR(4)  NOT NULL,
    emp\_no       INT      NOT NULL,
    from\_date    DATE     NOT NULL,
    to\_date      DATE     NOT NULL,
    KEY         (emp\_no),
    KEY         (dept\_no),
    FOREIGN KEY (emp\_no)  REFERENCES employees (emp\_no)    ON DELETE CASCADE,
                                   \-- ON UPDATE CASCADE??
    FOREIGN KEY (dept\_no) REFERENCES departments (dept\_no) ON DELETE CASCADE,
    PRIMARY KEY (emp\_no, dept\_no)  \-- might not be unique?? Need from\_date
    );

There are 24 records for this table.

##### Table "titles"

There is a one-to-many relationship between `employees` and `titles`. One employee has many titles (concurrently or at different dates). A `titles` record refers to one employee (via `emp_no`).

CREATE TABLE **titles** (
    emp\_no      INT          NOT NULL,
    title       VARCHAR(50)  NOT NULL,
    from\_date   DATE         NOT NULL,
    to\_date     DATE,
    KEY         (emp\_no),
    FOREIGN KEY (emp\_no) REFERENCES employees (emp\_no) ON DELETE CASCADE,
                         \-- ON UPDATE CASCADE??
    PRIMARY KEY (emp\_no, title, from\_date)
       \-- This ensures unique combination. 
       -- An employee may hold the same title but at different period
     );

There are 443,308 records for this table.

##### Table "salaries"

Similar structure to `titles` table. One-to-many relationship between `employees` and `salaries`.

CREATE TABLE **salaries** (
    emp\_no      INT    NOT NULL,
    salary      INT    NOT NULL,
    from\_date   DATE   NOT NULL,
    to\_date     DATE   NOT NULL,
    KEY         (emp\_no),
    FOREIGN KEY (emp\_no) REFERENCES employees (emp\_no) ON DELETE CASCADE,
    PRIMARY KEY (emp\_no, from\_date)
    );

There are 2,844,047 records for this table.

#### Stored Objects

No stored objects (view, procedure, function, trigger, event) defined. \[Shall try!\]
