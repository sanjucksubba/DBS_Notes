Unit III: Introduction to SQL
3.1 Overview of SQL Query Language
SQL (Structured Query Language) is the standard language for relational databases. It combines several components into a single language:
•	Data Definition Language (DDL): commands to define schemas, including creating, altering, and dropping relations, and specifying integrity constraints.
•	Data Manipulation Language (DML): commands to query and modify data — querying (SELECT), and updating (INSERT, DELETE, UPDATE).
•	Data Control: commands controlling access to data, including authorization specification (GRANT, REVOKE).
•	Transaction control: commands for specifying the beginning and ending of transactions.
SQL is a declarative language — the user specifies what data is wanted, not how to retrieve it; the query optimizer in the DBMS determines an efficient execution strategy.

3.2 SQL Data Definition
The set of relations in a database must be specified to the system using DDL. Key DDL elements include:
•	CREATE TABLE: defines a new relation, listing its attributes and their domains (data types), e.g., CREATE TABLE instructor (ID varchar(5), name varchar(20), dept_name varchar(20), salary numeric(8,2)).
•	Common SQL data types: char(n) — fixed-length character string; varchar(n) — variable-length character string; int — integer; numeric(p,d) — fixed-point number with p digits, d after the decimal point; date, time, timestamp for temporal values.
•	Integrity constraints in CREATE TABLE: PRIMARY KEY, FOREIGN KEY ... REFERENCES, UNIQUE, NOT NULL, and CHECK (a predicate that all rows must satisfy).
•	DROP TABLE: removes a relation entirely (both data and schema).
•	ALTER TABLE: adds or drops attributes from an existing relation (e.g., ALTER TABLE r ADD A D adds attribute A with domain D to relation r; existing tuples get a null value for A).

3.3 Basic Structure of SQL Queries
The basic structure of an SQL query consists of three clauses: SELECT, FROM, and WHERE.
SELECT clause: corresponds to the projection operation of relational algebra — it lists the attributes desired in the result of a query. Using SELECT * selects all attributes. By default, SQL does not eliminate duplicate rows; the DISTINCT keyword must be used to force the elimination of duplicates.
FROM clause: corresponds to the Cartesian-product operation — it lists the relations to be scanned in the evaluation of the query. If more than one relation is listed, the Cartesian product of all relations is computed.
WHERE clause: corresponds to the selection predicate of relational algebra — it consists of a predicate involving attributes from the relations in the FROM clause, and only tuples satisfying the predicate are retained.
Conceptually, a query is evaluated by: (1) generating the Cartesian product of the relations in FROM, (2) applying the predicates in WHERE to the result, and (3) projecting the result onto the attributes in SELECT. (Real implementations use far more efficient strategies, covered in Unit VI.)

3.4 Additional Basic Operations
•	Renaming (AS clause): both relations and attributes can be renamed using the AS clause, e.g., SELECT name AS instructor_name FROM instructor AS T — useful for clarity and for comparing a relation with itself.
•	Ordering of tuples (ORDER BY): displays the results of a query in sorted order, e.g., ORDER BY name; can sort in ascending (ASC, default) or descending (DESC) order, and on multiple attributes.
•	WHERE clause predicates: SQL includes a BETWEEN comparison operator to simplify range comparisons (e.g., salary BETWEEN 90000 AND 100000), and supports comparisons over tuples of values using the syntax (A1, A2) <op> (B1, B2).

3.5 Set Operations
SQL provides set operations to combine the results of two or more queries — each operand query must have the same number of attributes with compatible types. Each operation comes in two forms: with automatic duplicate elimination, and with ALL to retain duplicates.
•	UNION: combines the results of two queries, automatically eliminating duplicate tuples; UNION ALL retains duplicates.
•	INTERSECT: returns only tuples that appear in the results of both queries; INTERSECT ALL retains duplicates according to multiset semantics.
•	EXCEPT (also known as MINUS in some systems): returns tuples that are in the result of the first query but not in the result of the second; EXCEPT ALL retains duplicates accordingly.

3.6 Null Values
SQL allows the use of null values to indicate that the value either is unknown or does not exist. Nulls introduce special handling:
•	Arithmetic operations: any arithmetic expression involving a null value evaluates to null.
•	Comparisons: comparing any value with null using standard operators (=, <, etc.) yields a special truth value 'unknown' (not true or false).
•	Three-valued logic: SQL logical connectives (AND, OR, NOT) are extended to handle the 'unknown' value — e.g., (unknown OR true) = true, (unknown OR false) = unknown, (unknown AND false) = false. A WHERE clause predicate is satisfied only if it evaluates to true (rows evaluating to unknown or false are excluded).
•	Testing for null: SQL provides the special predicates IS NULL and IS NOT NULL to test for null values, since the comparison 'attribute = NULL' would itself evaluate to unknown.

3.7 Aggregate Functions
Aggregate functions take a collection (multiset) of values as input and return a single value. SQL provides five built-in aggregate functions:
•	avg — average value; COUNT — number of values; MIN — minimum value; MAX — maximum value; SUM — sum of values. COUNT(*) counts all rows including duplicates and nulls; other aggregates ignore null values in their input (except COUNT(*)).
•	GROUP BY clause: groups tuples based on the value of one or more attributes, and aggregate functions are applied independently to each group, producing one result row per group.
•	HAVING clause: applies a predicate to groups (formed by GROUP BY), analogous to how WHERE applies a predicate to individual tuples; predicates in HAVING are applied after the formation of groups, so aggregate functions may be used.

3.8 Nested Subqueries
SQL provides a mechanism for nesting subqueries — a subquery is a SELECT-FROM-WHERE expression that is nested within another query. A common use is to perform tests for set membership, set comparisons, and set cardinality.
•	Set membership: IN and NOT IN test for the (non-)existence of a tuple in a set produced by a subquery.
•	Set comparison: comparisons such as > SOME and > ALL test a value against a set produced by a subquery — '> SOME' is true if it is greater than at least one member, while '> ALL' is true if it is greater than every member.
•	Test for empty relations: the EXISTS construct returns true if the argument subquery is non-empty; NOT EXISTS returns true if the subquery is empty.
•	Test for absence of duplicate tuples: the UNIQUE construct tests whether a subquery has any duplicate tuples in its result.
•	Subqueries in the FROM clause: SQL allows a subquery expression to be used in the FROM clause, treated as a derived relation (often given a name using AS).
•	WITH clause (common table expressions): provides a way of defining a temporary named relation whose definition is available only to the query in which it occurs, making complex queries easier to write and understand.
•	Scalar subqueries: a subquery that returns only a single attribute and a single tuple can be used anywhere a single value is expected.

3.9 Modification of the Database
In addition to retrieving data, SQL provides commands to modify the contents of the database:
Deletion (DELETE): removes tuples from a relation that satisfy a given WHERE condition; if the WHERE clause is omitted, all tuples are deleted. Note that DELETE operates on whole tuples and cannot remove values from only particular attributes.
Insertion (INSERT): either specifies the tuple to be inserted (INSERT INTO ... VALUES ...) or writes a query whose result is a set of tuples to be inserted. Attribute values for new tuples must be members of the corresponding attribute's domain.
Update (UPDATE): changes values in some existing tuples without changing all values, using the SET clause to specify new values and an optional WHERE clause to specify which tuples to update. The CASE construct can be used within UPDATE to apply conditional updates.
