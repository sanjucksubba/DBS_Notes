Unit IV: Intermediate and Advanced SQL

4.1 Join Expressions

Join operations take two relations and return a relation combining tuples from both, based on a join condition. SQL supports several types of joins:

•	Inner join: returns only those tuples where the join condition is satisfied in both relations. The NATURAL JOIN matches tuples based on equality of all attributes with the same name, while JOIN ... ON allows an arbitrary join condition.

•	Left outer join: takes all tuples in the left relation that did not match any tuple in the right relation, pads them with nulls for attributes from the right relation, and adds them to the result of the inner join.

•	Right outer join: symmetric to left outer join — preserves unmatched tuples from the right relation, padding with nulls for the left relation's attributes.

•	Full outer join: preserves unmatched tuples from both relations, padding with nulls as appropriate.

•	Join conditions: can use ON to specify a general predicate, or USING (list of attributes) for a shorthand when joining on attributes that share the same name.

4.2 Views

A view is a relation that is not part of the logical model of the database but is made visible to a user as a virtual relation, defined using CREATE VIEW <name> AS <query expression>. Views can be used to:

•	Hide certain data (e.g., sensitive attributes such as salary) from users who should not have direct access.

•	Create a custom, simplified view of the database matching a particular user's perspective.

•	Materialized views: in some database systems, the result of a view can be physically stored — this is called a materialized view; such views must be kept up-to-date when the underlying (base) relations are modified, a process called view maintenance.

•	Update of a view: an update on a view defined on a single relation can in general be translated into an update on the underlying relation, but if the view involves joins or aggregates, updates may not be allowed or may have ambiguous translations.

4.3 Transactions

A transaction consists of a sequence of query and/or update statements, and is a unit of atomicity from the perspective of database consistency. In SQL, a transaction begins implicitly when an SQL statement is executed, and is completed by one of:

•	COMMIT WORK: commits the current transaction — all updates made by the transaction become permanent.

•	ROLLBACK WORK: causes the current transaction to be rolled back — all updates performed by the SQL statements in the transaction are undone.
Many SQL implementations, by default, treat each individual SQL statement as a separate transaction that is automatically committed (autocommit mode); this can be disabled to group multiple statements into a single transaction. Transactions are explored in much greater depth in Unit VII.

4.4 Integrity Constraints

Integrity constraints ensure that changes made to the database by authorized users do not result in a loss of data consistency. They guard against accidental damage to the database. Common constraints (beyond keys, covered in Unit II) include:

•	NOT NULL: specifies that a null value is not allowed for an attribute.

•	UNIQUE: specifies that an attribute (or set of attributes) forms a candidate key — values must be unique (nulls are typically permitted unless combined with NOT NULL).

•	CHECK (predicate): allows an arbitrary predicate to be specified that each tuple in a relation must satisfy.

•	Referential integrity (FOREIGN KEY): ensures that a value appearing in one relation for a given set of attributes also appears in a certain set of attributes (typically the primary key) in another relation. Referential integrity is checked when a row is inserted/updated in the referencing table, and when a row is deleted/updated in the referenced table — actions on violation can be specified using CASCADE, SET NULL, etc.

4.5 SQL Data Types and Schemas

In addition to the basic built-in types (char, varchar, int, numeric, date, time, timestamp), SQL supports user-defined types:

•	CREATE TYPE: defines a new domain/distinct type based on an existing type, often with a CHECK constraint, allowing more meaningful and consistent type usage across a schema (e.g., CREATE TYPE dollars AS numeric(12,2)).

•	CREATE DOMAIN: similar to CREATE TYPE, defines a domain that can include constraints which are applied to all attributes defined on that domain.

•	Schemas, catalogs, and environments: a full name for a relation is actually a three-part name — catalog.schema.relation — allowing different applications/users to work with separate schemas without name clashes.

4.6 Index Definition in SQL

Most SQL implementations support a CREATE INDEX command to create indices, although this is not part of the SQL standard. Syntax typically takes the form: CREATE INDEX <index-name> ON <relation-name> (<attribute-list>). Indices speed up retrieval of records matching conditions on the indexed attributes — the underlying data structures and trade-offs are covered in depth in Unit VI.

4.7 Authorization

SQL supports a privilege-based model of authorization, allowing the database administrator (or owners of relations) to grant and revoke privileges to users:

•	Forms of authorization on data: SELECT, INSERT, UPDATE, and DELETE privileges, each allowing the corresponding operation to be performed on specified relations (or specific columns thereof).

•	Other authorization forms: REFERENCES privilege (to declare foreign key constraints referencing a relation), and authorization to modify the schema itself (e.g., adding/dropping attributes via ALTER, or creating/dropping indices).

•	The GRANT statement is used to confer authorization (e.g., GRANT SELECT ON instructor TO U1, U2, U3), and the REVOKE statement removes previously granted authorization. WITH GRANT OPTION allows a user receiving a privilege to further grant it to other users.

•	Role-based authorization: privileges can be granted to roles, and roles can then be granted to users, simplifying the management of authorization for groups of users with similar needs.

4.8 Accessing SQL from a Programming Language

SQL statements can be embedded within or called from general-purpose programming languages, allowing applications to combine the power of SQL with the procedural capabilities of a host language.

•	Embedded SQL: SQL statements are embedded within a program written in a host language, identified by a special prefix (e.g., EXEC SQL); a preprocessor converts the embedded SQL into normal procedure calls before compilation.

•	Dynamic SQL: allows a program to construct an SQL query as a character string at runtime, and submit it for execution.

•	ODBC and JDBC: ODBC (Open Database Connectivity) is a C-language application program interface standard for connecting to a database server and submitting commands; JDBC is the corresponding standard for Java. Both use a connection object to establish a session with the database, statement objects to send queries/updates, and result-set objects to process returned tuples.

4.9 Functions and Stored Procedures

SQL allows the definition of functions and procedures — encapsulated code blocks stored within the database and reusable across queries and applications.

•	Functions: can return a single value (scalar) or, in some systems, a table (table-valued functions); they can be invoked wherever an expression of the appropriate type is permitted.

•	Procedures: can be invoked using a CALL statement, or from within other procedures or functions; procedures and functions can have IN, OUT, and INOUT parameters.

•	SQL/PSM (Persistent Stored Modules) extensions provide procedural constructs such as compound statements (BEGIN...END), variable declarations, looping constructs (WHILE, REPEAT, FOR), and conditional statements (IF-THEN-ELSE, CASE) for use within functions and procedures.

•	External language routines: functions and procedures can also be implemented in general-purpose programming languages (e.g., Java, C, Python), executed by the database in a tightly integrated way, which can be more efficient for complex computations than SQL-only logic.

4.10 Triggers

A trigger is a statement that the system executes automatically as a side effect of a modification (insert, delete, or update) to the database. To design a trigger, two requirements must be met:

•	Specify the conditions under which the trigger is to be executed.

•	Specify the actions to be taken when the trigger executes.

Triggers can be defined to fire BEFORE, AFTER, or INSTEAD OF the triggering event, and at the row level (once per affected row) or statement level (once per statement). The special variables OLD and NEW (or REFERENCING ... OLD AS ... / NEW AS ...) can be used to refer to the old and new values of a tuple being modified. Although powerful, triggers should be used with caution, since complex interactions between multiple triggers can make system behavior difficult to understand and debug.

4.11 Recursive Queries

Some queries, such as finding all ancestors of an entity in a hierarchy (e.g., a course prerequisite chain), require recursion that cannot be expressed by a non-recursive SQL query. SQL provides recursive view/query definitions using the WITH RECURSIVE clause, which works by:

•	Defining a base case — typically a non-recursive subquery that establishes the starting set of tuples.

•	Defining a recursive step — a query that refers to the recursive view/relation itself, which is repeatedly evaluated and unioned with the result until no new tuples are generated (a fixed point is reached).

Care must be taken to ensure that recursive definitions are monotonic (so they terminate) — e.g., recursion combined with aggregation or negation can lead to ambiguous results and is typically restricted by the SQL standard.

4.12 Advanced Aggregation Features

Beyond basic GROUP BY and HAVING (covered in Unit III), SQL provides further aggregation capabilities for analytical/OLAP-style queries:

•	Ranking functions: assign a rank to each tuple within an ordering specified using ORDER BY in an OVER clause — e.g., RANK, DENSE_RANK (gives the same rank to ties without leaving gaps), ROW_NUMBER, and PERCENT_RANK.

•	Windowing: the OVER clause can also include a PARTITION BY clause, which partitions the relation into groups (similar to GROUP BY) but allows multiple rows per group to remain in the output, each annotated with its rank or aggregate value computed over a 'window' of rows.

•	PIVOT and UNPIVOT: in some SQL dialects, PIVOT allows the conversion of data from rows to columns (cross-tabulation), while UNPIVOT performs the reverse transformation.

•	Extended grouping constructs: features such as CUBE and ROLLUP generate multiple groupings in a single query, useful for producing multi-dimensional summary reports common in data warehousing/OLAP.
 
