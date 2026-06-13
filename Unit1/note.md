Unit I: Introduction to Database Systems

1.1 View of Data
A database system provides users with an abstract view of data — it hides certain details of how data is stored and maintained. This abstraction is achieved through three levels:
•	Physical level (lowest): describes how data is actually stored — bytes, blocks, files, indexes.
•	Logical level: describes what data is stored and the relationships among data. Database administrators work at this level.
•	View level: describes only part of the database, simplifying interaction for end users by hiding details.
These levels are tied together by the concept of a schema (the overall design/structure of the database) versus an instance (the actual data stored at a particular moment). A data model is a collection of conceptual tools for describing data, relationships, semantics, and constraints (e.g., relational model, entity-relationship model, object-based model, semi-structured/XML model).

1.2 Purpose of Database Systems
Before DBMSs, organizations stored data in file-processing systems using application programs that directly read/wrote files. This approach had major drawbacks:
•	Data redundancy and inconsistency — same information duplicated in multiple files, causing wasted space and inconsistent copies.
•	Difficulty in accessing data — every new query/task required writing a new program.
•	Data isolation — data scattered in various files of different formats, hard to write programs to retrieve appropriate data.
•	Integrity problems — constraints (e.g., balance must be positive) buried in program code rather than stated explicitly, making them hard to enforce or change.
•	Atomicity problems — failures (e.g., power outage mid-transaction) can leave data in an inconsistent state.
•	Concurrent-access anomalies — multiple users updating data simultaneously can lead to inconsistencies without proper control.
•	Security problems — difficult to give users access to only the data they need.
A DBMS addresses all these problems by providing centralized control, data abstraction, and mechanisms for integrity, concurrency, recovery, and security.

1.3 History of Database Systems
•	1950s–60s: Magnetic tape used for data storage — sequential access only.
•	Late 1960s–70s: Hard disks allowed direct (random) access; network and hierarchical data models emerged (e.g., IBM IMS).
•	1970: E.F. Codd introduced the relational model — a landmark paper that defined data independence and set-based query processing.
•	1980s: Relational databases (System R, Oracle, DB2, Ingres) became commercially dominant; SQL standardized.
•	1990s: Object-oriented and object-relational databases; data warehousing and OLAP; widespread use of SQL for transaction processing and decision support.
•	2000s: Rise of the Web led to semi-structured data (XML), and very large-scale systems (Big Data) drove the creation of NoSQL/key-value/column/document databases for horizontal scalability.
•	Present: A mix of relational, NoSQL, NewSQL, and cloud-based/distributed database systems coexist, chosen based on application requirements.

1.4 Database-System Applications
Databases are used across nearly every domain. Common categories include:
•	Enterprise Information: sales, accounting, human resources, manufacturing, online retail.
•	Banking and Finance: account records, transactions, loans, online banking, stock and bond trading.
•	Universities: registration, grades, course information.
•	Airlines: reservations, schedules.
•	Telecommunications: call records, billing, prepaid card balances.
•	Healthcare: patient records, billing, medical history.
•	Online retailers: order tracking, personalized recommendations.
•	Document/text databases: large collections of text documents (legal, news) with full-text search capability.

1.5 Database Languages
A DBMS provides two types of language constructs:
Data Definition Language (DDL): used to define the database schema (e.g., CREATE, ALTER, DROP). The output of compiling DDL statements is stored in a data dictionary (system catalog) — a special set of tables containing metadata, such as schema, integrity constraints, and authorization information.
Data Manipulation Language (DML): used to express queries and updates (e.g., SELECT, INSERT, UPDATE, DELETE). DMLs can be procedural (specify what data and how to get it, e.g., relational algebra) or declarative/non-procedural (specify what data is needed without specifying how to get it, e.g., SQL — also called declarative DML).
In practice, the term 'query language' is commonly used as a synonym for DML, although technically queries are only part of DML.

1.6 Database Design
The database design process broadly involves the following phases:
1.	Requirements analysis: understand what the application needs to do — what data must be stored and what operations need to be performed on it.
2.	Conceptual design: produce a high-level description of the data, often using the Entity-Relationship (E-R) model, capturing entities, relationships, and constraints.
3.	Logical design: translate the conceptual schema into the data model of the chosen DBMS (e.g., mapping an E-R diagram into a relational schema of tables and columns).
4.	Physical design: specify the physical features of the database such as file organization and indexing structures for efficient performance.
Good design also involves verifying that the schema satisfies desirable properties, particularly avoiding unnecessary duplication of data (addressed in depth in Unit V — normalization).

1.7 Database Engine
The database engine is the core software component that provides several key services. Its major components are:
Storage manager: provides the interface between the low-level data stored in the database and the application programs/queries. It is responsible for interaction with the file system, efficient storing/retrieving/updating of data, and includes the authorization and integrity manager, transaction manager, file manager, and buffer manager.
Query processor: translates DML statements into low-level instructions the storage manager understands. Includes the DDL interpreter, DML compiler (which performs query optimization to pick an efficient execution plan), and query evaluation engine (which executes the plan).
Transaction manager: ensures the database remains in a consistent state despite system failures and concurrent transaction execution, enforcing ACID properties.

1.8 Database and Application Architecture
Database applications are typically split into front-end and back-end portions, often arranged in tiers:
•	One-tier (centralized) architecture: the database, application logic, and user interface all run on a single machine — typical of early mainframe systems.
•	Two-tier architecture: the application resides at the client machine and communicates with the database server directly via query-language statements, often through an API such as ODBC or JDBC.
•	Three-tier architecture: the client machine acts as a front end only and does not contain direct database calls. Instead, the client communicates with an application server (which contains the business logic), which in turn communicates with the database system. This is the dominant architecture for Web applications, since it adds an extra layer of security and scalability and centralizes business rules.

1.9 Database Users and Administrators
Database systems involve people with different roles and levels of interaction with the system.
Users (by interaction type)
•	Naive users: interact with the system by invoking pre-written application programs (e.g., a bank teller using a transaction form).
•	Application programmers: write application programs using tools such as Rapid Application Development (RAD) tools, or directly using programming languages with embedded database calls.
•	Sophisticated users: interact with the system using a database query language directly, without writing programs — e.g., analysts who form complex queries to extract data.
•	Specialized users: write specialized database applications that may not fit into the traditional data-processing framework, such as CAD systems, knowledge-base/expert systems, or systems that store complex data types (graphics, audio, video).
Database Administrator (DBA)
The DBA is the person/team with central control over the system, with responsibilities including:
•	Schema definition — creating the original database schema.
•	Storage structure and access-method definition.
•	Schema and physical-organization modification — altering the schema or physical organization to reflect the changing needs of the organization, or to improve performance.
•	Granting of authorization for data access — controlling which parts of the database various users can access.
•	Routine maintenance — periodic backups, ensuring enough disk space, monitoring jobs, and upgrading software.
