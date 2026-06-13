Unit II: Database Design

2.1 Overview of the Design Process

The design process for a database application can be divided into six phases:

1.	Requirements analysis — interviewing stakeholders to determine data requirements.
2.	Conceptual database design — creating a high-level data model (e.g., E-R diagram) independent of any specific DBMS.
3.	Logical database design — converting the conceptual schema into the data model of the chosen DBMS (typically the relational model), producing a relational database schema.
4.	Schema refinement — analyzing the logical schema to identify and correct potential problems, primarily through normalization (covered in Unit V).
5.	Physical database design — deciding on physical features such as file organization and indices.
6.	Application and security design — designing the application programs and security/access controls around the database.
   


2.2 Relational Modeling

The relational model represents data as a collection of relations (tables). Key terminology:

•	Relation: a table with rows and columns.

•	Tuple: a row of a relation, representing a single record.

•	Attribute: a column of a relation, representing a property of the entity.

•	Domain: the set of permitted values for each attribute.

•	Relation schema: the structure of the relation, e.g., instructor(ID, name, dept_name, salary).

•	Relation instance: the actual set of tuples in a relation at a given point in time — a relation is unordered (no implicit ordering of tuples).

Each attribute value must, in general, be atomic (indivisible) — multivalued or composite attributes from an E-R diagram must be decomposed into atomic values when mapped to the relational model.


2.3 The Entity-Relationship Model

The E-R model views the real world as a set of basic objects (entities) and relationships among them. It is widely used for conceptual database design.

•	Entity: a 'thing' or 'object' in the real world that is distinguishable from other objects (e.g., a specific person, course, or account).

•	Entity set: a set of entities of the same type that share the same properties/attributes (e.g., all instructors).

•	Relationship: an association among several entities (e.g., an instructor advises a student).

•	Relationship set: a set of relationships of the same type.

•	Degree of a relationship set: the number of entity sets that participate — binary (two entity sets) is most common, but ternary and higher-degree relationships are possible.

E-R diagrams use rectangles for entity sets, diamonds for relationship sets, and lines/ovals to connect attributes.


2.4 Complex Attributes

Attributes in the E-R model can be classified by structure:

•	Simple vs. composite: a simple attribute cannot be divided further (e.g., age); a composite attribute can be divided into smaller sub-parts (e.g., name divided into first_name and last_name).

•	Single-valued vs. multivalued: most attributes are single-valued, but a multivalued attribute can have multiple values for a single entity (e.g., a person can have several phone_number values).

•	Derived attribute: a value that can be computed/derived from other related attributes or entities (e.g., age can be derived from date_of_birth and the current date). The stored attribute on which it depends is sometimes called a base attribute.

An attribute (or combination) takes a null value when an entity does not have a value for it, or the value is unknown/not applicable.


2.5 Mapping Cardinalities

Mapping cardinality (or cardinality ratio) expresses the number of entities to which another entity can be associated via a relationship set. For binary relationship sets between entity sets A and B, the possible mapping cardinalities are:

•	One-to-one (1:1): an entity in A is associated with at most one entity in B, and vice versa.

•	One-to-many (1:N): an entity in A can be associated with any number of entities in B, but an entity in B is associated with at most one entity in A.

•	Many-to-one (N:1): the reverse of one-to-many.

•	Many-to-many (M:N): an entity in A can be associated with any number of entities in B, and vice versa.
Cardinality constraints are chosen based on the real-world situation being modeled and directly affect how relationships are mapped to relational schemas.


2.6 Primary Key

A primary key is a minimal set of attributes (a candidate key) that can uniquely identify a tuple within its relation/entity set. Related concepts:

•	Superkey: any set of one or more attributes whose values uniquely determine each entity/tuple.

•	Candidate key: a minimal superkey — no proper subset of it is also a superkey. A relation may have multiple candidate keys.

•	Primary key: the candidate key chosen by the database designer as the principal means of identifying tuples within a relation. By convention it is listed first and often underlined in the schema.

•	Foreign key: an attribute (or set of attributes) in one relation that refers to the primary key of another relation, enforcing referential integrity between the two relations.

For weak entity sets (entities that do not have enough attributes to form their own primary key), the primary key is formed by combining the partial key of the weak entity with the primary key of the identifying (owner/strong) entity set.


2.7 Removing Redundant Attributes in Entity Sets

When converting E-R diagrams to relational schemas, redundancy can occur — particularly when relationships are mapped in a way that duplicates information already captured by another entity's primary key or by a relationship's foreign key. Designers should:

•	Avoid storing the same descriptive attribute in multiple entity sets when it logically belongs in one place and can be obtained through a relationship/foreign key reference.

•	Identify attributes that depend on (are functionally determined by) other attributes already present via a relationship, and remove the redundant copies — this overlaps with normalization concepts covered later in Unit V.

Removing such redundancies early reduces storage waste and avoids data-inconsistency problems before schema refinement (normalization) is even applied.


2.8 Reducing E-R Diagrams to Relational Schemas

A database designed using the E-R model can be represented by a collection of relations using the following standard mapping rules:

•	Strong entity set: becomes a relation with the same attributes; the entity set's primary key becomes the relation's primary key.

•	Weak entity set: becomes a relation that includes a column for the primary key of the identifying (strong) entity set, plus its own discriminator/partial key; the primary key of the relation is the combination of the strong entity's primary key and the weak entity's discriminator.

•	Relationship set: in general, becomes a relation whose attributes are the union of the primary keys of the participating entity sets plus any descriptive attributes of the relationship itself. The primary key depends on the cardinality:

◦	For many-to-many relationships, the primary key is the combination of both participating entities' primary keys.

◦	For one-to-many or many-to-one relationships, the relationship can often be represented by simply adding the primary key of the 'one' side as a foreign key attribute to the relation on the 'many' side, rather than creating a separate relation.

◦	For one-to-one relationships, either entity's relation may include the other's primary key as a foreign key (with a unique constraint).

•	Multivalued attributes: become a separate relation containing the entity's primary key plus the multivalued attribute's value, with the combination forming the primary key of this new relation.

•	Composite attributes: are flattened — each component becomes a separate attribute of the relation.


2.9 Extended E-R Features

The basic E-R model can be extended with additional features to capture more complex applications:

Specialization: the process of designating subgroupings within an entity set that have distinguishing characteristics (top-down design), e.g., an 'account' entity specialized into 'savings_account' and 'checking_account'.

Generalization: the reverse of specialization — a bottom-up process combining a number of entity sets that share common features into a higher-level (generalized) entity set.

Attribute inheritance: a lower-level entity set inherits all the attributes and relationship participation of the higher-level entity set.

Constraints on generalization: include whether membership in a lower-level entity set is total (every higher-level entity must belong to some lower-level entity set) or partial, and whether sets are disjoint (an entity can belong to only one lower-level set) or overlapping.

Aggregation: an abstraction in which relationship sets are treated as higher-level entity sets, allowing them to participate in further relationships — used when a relationship between relationships needs to be expressed.


2.10 Entity-Relationship Design Issues

Several subtle design decisions arise when modeling with E-R diagrams:

•	Use of entity sets vs. attributes: deciding whether a concept (e.g., 'phone') should be modeled as an attribute of an entity or as its own entity set (the latter is preferred if it has its own attributes, or participates in relationships with other entities, or if there can be multiple values).

•	Use of entity sets vs. relationship sets: it can sometimes be unclear whether a real-world concept is best expressed by an entity set or a relationship set — a useful guideline is that a relationship set should be used to describe an action that occurs between entities.

•	Binary vs. n-ary relationship sets: although most relationships are binary, non-binary (n-ary) relationships can sometimes arise; these can often, but not always, be redesigned as multiple binary relationship sets.

•	Placement of relationship attributes: deciding whether an attribute belongs to an entity or to the relationship connecting entities (e.g., should 'date' belong to the relationship 'borrows' rather than to 'book' or 'member').


2.11 Alternative Notations for Modeling Data

Besides the classic Chen notation (rectangles for entities, diamonds for relationships, ovals for attributes), several alternative notations are widely used in practice:

•	Unified Modeling Language (UML) class diagrams: represent entity sets as classes, with attributes and relationships shown via associations — UML is widely used in software engineering for both database and application design.

•	Crow's foot notation: a popular notation for representing cardinality constraints directly on the connecting lines using symbols resembling a crow's foot (for 'many') and circles/bars (for optional/mandatory participation), commonly used in many database design tools.

These notations convey largely the same underlying information as the E-R model but differ in graphical conventions; designers should be able to read and translate between them.


2.12 Other Aspects of Database Design

Beyond data modeling itself, database design also needs to consider:

•	Data constraints and business rules that must be enforced by the schema (beyond simple keys), such as domain constraints and check conditions.


•	Documentation of the design — design choices, naming conventions and assumptions should be documented for future maintainability.

•	User views — designing how different categories of users will interact with and view the data (tying back to the view-level abstraction from Unit I).

•	Performance considerations — although detailed physical design comes later, designers should keep in mind how the logical design will affect future indexing and query performance.


2.13 Atomicity, Consistency, Isolation and Durability

ACID properties are a set of properties that guarantee that database transactions are processed reliably, and they are central to the design of any transactional database system:

Atomicity: ensures that a transaction is treated as a single, indivisible unit — either all of its operations are reflected properly in the database, or none are (all-or-nothing).

Consistency: ensures that a transaction takes the database from one consistent state to another — execution of a transaction in isolation must preserve the consistency of the database (all defined integrity constraints must hold).

Isolation: ensures that even though multiple transactions may execute concurrently, the system guarantees that, for every pair of transactions, one transaction's execution does not affect the other — each transaction is unaware of other transactions executing concurrently.

Durability: ensures that once a transaction completes successfully (commits), its changes to the database persist, even if there are system failures.
These properties are introduced conceptually here as a foundation for database design, and are explored in much greater depth — along with implementation mechanisms — in Unit VII.
