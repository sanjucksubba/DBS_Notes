Unit V: Relational Database Design
5.1 Features of Good Relational Designs
The overall goal of relational database design is to generate a set of relation schemas that allows storage of information without unnecessary redundancy, while also allowing information to be retrieved easily and accurately. A poorly designed schema can result in:
•	Redundancy: the same piece of information being stored repeatedly, leading to wasted storage and increased risk of inconsistency.
•	Update anomalies: if one copy of repeated data is updated but not all copies, the database becomes inconsistent.
•	Insertion anomalies: difficulty inserting certain facts because other unrelated information is also required, or because doing so requires creating spurious values for some attributes.
•	Deletion anomalies: deleting data about one fact may inadvertently cause the loss of other, unrelated information.
A well-designed relational schema avoids these anomalies, and a key tool for measuring whether a design avoids them is functional dependency theory and normalization.

5.2 Decomposition Using Functional Dependencies
Decomposition is the process of splitting a relation schema into two or more smaller schemas to remove undesirable properties (redundancy, anomalies) while preserving the information content of the original schema.
•	A decomposition must be lossless — it should be possible to reconstruct the original relation by joining the decomposed relations, without generating spurious tuples (a lossless-join decomposition).
•	A decomposition is ideally dependency-preserving — the functional dependencies of the original relation should be enforceable on the decomposed relations without needing to join them back together to check the constraint.
Decomposition is guided by functional dependencies — for example, decomposing on a functional dependency that violates a normal form into two relations, one containing the determinant and dependent attributes, and the other retaining the determinant plus the remaining attributes.

5.3 Normal Forms
Normal forms are a series of criteria used to evaluate relation schemas, each addressing a particular type of redundancy/anomaly. Common normal forms (with their primary criteria) include:
•	First Normal Form (1NF): the domains of all attributes are atomic — each attribute contains only single, indivisible values (no repeating groups or multivalued attributes).
•	Second Normal Form (2NF): the relation is in 1NF, and every non-prime attribute is fully functionally dependent on the (whole) candidate key — i.e., no partial dependencies of a non-key attribute on part of a composite key.
•	Third Normal Form (3NF): the relation is in 2NF, and there is no transitive dependency of a non-prime attribute on a candidate key — i.e., for every non-trivial functional dependency, either the left side is a superkey, or the right side consists only of prime (candidate-key) attributes.
•	Boyce-Codd Normal Form (BCNF): a stricter version of 3NF — for every non-trivial functional dependency A to B, A must be a superkey of the relation. BCNF eliminates more redundancy than 3NF, but a BCNF decomposition is not always dependency-preserving.

5.4 Functional-Dependency Theory
A functional dependency (FD), written A → B, is a constraint stating that the value of attribute set A uniquely determines the value of attribute set B (whenever two tuples agree on A, they must agree on B).
•	Closure of a set of functional dependencies (F+): the set of all functional dependencies that are logically implied by F.
•	Armstrong's axioms: a sound and complete set of inference rules for computing F+ — reflexivity (if B is a subset of A, then A → B), augmentation (if A → B, then AC → BC for any C), and transitivity (if A → B and B → C, then A → C).
•	Closure of an attribute set (A+): the set of all attributes that are functionally determined by A given F — used to test whether A is a superkey (A is a superkey if and only if A+ contains all attributes of the relation), and to test whether a given FD is in F+.
•	Canonical (minimal) cover: a simplified, equivalent set of functional dependencies in which each FD has no extraneous attributes on the left-hand side, the right-hand side is a single attribute, and no FD can be removed without changing the closure — used to simplify the analysis and to drive decomposition algorithms.

5.5 Algorithms for Decomposition Using Functional Dependencies
Several algorithmic procedures exist to decompose a relation schema into a set of relations satisfying a desired normal form while preserving certain properties:
•	BCNF decomposition algorithm: repeatedly finds a functional dependency A → B that violates BCNF (where A is not a superkey) in a relation, and decomposes that relation into two relations — one containing A and B, and the other containing A and all attributes except those in B — repeating until all resulting relations are in BCNF. This guarantees a lossless-join decomposition, but may not preserve all dependencies.
•	3NF synthesis algorithm: starts from a canonical cover of the functional dependencies and constructs a relation schema for each FD in the cover (plus, if necessary, an extra relation containing just a candidate key of the original relation). This algorithm guarantees both a lossless-join and dependency-preserving decomposition into 3NF, though some redundancy consistent with 3NF (but not BCNF) may remain.
The choice between BCNF and 3NF decomposition involves a trade-off between eliminating all redundancy (BCNF) and preserving the ability to check all dependencies without joins (3NF).

5.6 Decomposition Using Multivalued Dependencies
Some redundancy cannot be detected using functional dependencies alone, and is instead captured by multivalued dependencies (MVDs).
•	A multivalued dependency, written A →→ B, states that for each value of A, the set of associated values of B is independent of (does not depend on) the values of all other attributes of the relation — given two tuples agreeing on A, their B-values and remaining-attribute-values can be 'swapped' to produce two more valid tuples.
•	Every functional dependency is also a multivalued dependency (trivially), but the converse is not true — MVDs can exist even where no corresponding FD holds, capturing a different kind of redundancy (often arising when an entity has two or more independent multivalued attributes/relationships).
•	MVDs are used in the definition of Fourth Normal Form, and decomposing based on a non-trivial MVD that violates 4NF (splitting into two relations as with BCNF, but driven by the MVD rather than an FD) removes this type of redundancy.

5.7 More Normal Forms
•	Fourth Normal Form (4NF): a relation is in 4NF if, for every non-trivial multivalued dependency A →→ B, A is a superkey. 4NF eliminates redundancy caused by independent multivalued facts about an entity that BCNF alone cannot remove.
•	Higher normal forms (e.g., Fifth Normal Form / Project-Join Normal Form) deal with even more subtle forms of redundancy arising from join dependencies, but are rarely needed in practical database design — in practice, achieving 3NF or BCNF is usually sufficient.

5.8 Atomic Domains and First Normal Form
A domain is atomic if elements of the domain are considered to be indivisible units — a relational schema is in First Normal Form if the domains of all attributes of the relation are atomic. Non-atomic values (such as composite attributes, sets, or arrays stored within a single field) complicate storage and the expression of queries, and SQL standard types are intended to be atomic. However, complex data such as composite attributes, multivalued attributes, and nested structures can be encoded using atomic-valued domains by techniques such as creating additional relations, at the cost of some loss of intuitive value when querying.

5.9 Database-Design Process
Putting it together, the overall relational database design process generally proceeds as follows:
11.	Begin with a conceptual model (e.g., an E-R diagram) developed from requirements analysis.
12.	Map the conceptual model to a set of relation schemas using the standard E-R-to-relational mapping rules (Unit II).
13.	Identify functional dependencies that should hold among attributes of the resulting relations, based on knowledge of the enterprise being modeled.
14.	Apply normalization (via decomposition algorithms) to check and refine the schema so that it satisfies the desired normal form (typically at least 3NF or BCNF), removing redundancy and anomalies.
15.	Consider denormalization where appropriate — in some cases, designers may deliberately accept some redundancy from a lower normal form to improve the performance of frequently-run queries, as a trade-off against the risk of inconsistency.

5.10 Modelling Temporal Data
Temporal data refers to data that varies over time — for example, an employee's address or salary may have a history of different values, each valid during a particular period.
•	Time-stamping: a common approach is to add valid-time attributes (e.g., valid_from and valid_to columns) to a relation, so each tuple represents a fact that was true during a specific time interval, rather than only the current state.
•	Modeling challenges: representing temporal data raises issues such as how to define keys (since the same entity may now have multiple tuples representing its history), how to handle queries that need to find the value valid 'as of' a particular time, and how to handle updates that should create a new historical record rather than overwrite an existing one.
•	Some database systems and SQL extensions provide built-in support for temporal tables, automatically maintaining history and supporting queries 'as of' a point in time, simplifying the application logic that would otherwise be required.
