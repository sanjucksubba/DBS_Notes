Unit VI: Indexing and Query Processing
6.1 Basic Concepts of Indexing
An index is an auxiliary data structure that is used to speed up access to data based on the value of one or more attributes (the search key), avoiding the need for a costly full scan of the relation.
•	Search key: an attribute, or set of attributes, used to look up records in a file (note: this is a more general term than 'primary key' — a search key can be any attribute(s) useful for lookup).
•	Every index has an associated overhead in storage space and in the time required to maintain it (insertions, deletions, and updates to the indexed relation must also update the index), so the choice of which attributes to index involves a trade-off between query speedup and update overhead.
•	Index evaluation metrics: include the access types it supports efficiently (point queries, range queries), access time, insertion/deletion time, and space overhead.

6.2 Indexing Structures
Two fundamentally different approaches to organizing index structures are widely used:
Ordered indices (B+-tree based): search keys are stored in sorted order, and the index is organized as a balanced tree structure (typically a B+-tree). B+-trees keep all data pointers at the leaf level, with internal nodes guiding the search, and they self-balance to keep all leaf nodes at the same depth, guaranteeing logarithmic lookup, insertion, and deletion time even as the file grows or shrinks.
Hash indices: search keys are organized using a hashing function that maps a search-key value into a bucket (a unit of storage, typically a disk block) where the corresponding record can be found. Hash indices provide very fast (constant-time, on average) lookups for exact-match (point) queries, but are not useful for range queries since logically related search-key values are not necessarily stored close together.
Bitmap indices are another structure, particularly useful for attributes that take on a small number of distinct values, where a bitmap (bit array) is created for each value, with one bit per record indicating whether that record has that value — bitmap indices can be combined efficiently using bitwise AND/OR operations to answer queries with multiple conditions.

6.3 Ordered and Unordered Indices
Indices can be classified based on the ordering of the underlying file (the file containing the actual records):
Clustering (primary) index: the search key also defines the sequential order of the file — since a file can be sorted in only one way, there can be at most one clustering index per relation. When the index is on a non-key field that the file is sorted on, it is sometimes called a clustering index in the strict sense, and records with the same value can be grouped into 'buckets' to avoid repeating the search-key value for every record.
Non-clustering (secondary) index: the search key specifies an order different from the sequential order of the file — there can be several secondary indices (also called non-clustering indices) on a relation, in addition to at most one clustering/primary index.
•	Dense index: an index record (entry) appears for every search-key value that occurs in the file (every record in the data file has a corresponding entry in the index).
•	Sparse index: index records are created for only some of the search-key values — a sparse index can only be used if the relation is sorted on the search key, and generally requires less space and maintenance overhead than a dense index, at the cost of potentially being slower for some lookups.

6.4 Indexing of Temporal and Spatial Data
Standard B+-tree and hash indices, which are designed for single-dimensional (totally ordered) keys, do not work well for temporal data (which involves time intervals/ranges) or spatial data (which involves multi-dimensional coordinates such as points, lines, and regions).
•	Temporal indexing: queries on temporal data often involve finding all records whose valid-time interval overlaps with a given query interval — specialized structures are used to efficiently support such interval-overlap queries, which a simple B+-tree on a single timestamp attribute cannot handle well.
•	Spatial indexing: structures such as the R-tree (and its variants) are designed to index multi-dimensional data by grouping nearby objects and representing them with their minimum bounding rectangle at the next higher level of the tree — this allows efficient processing of spatial range queries (e.g., 'find all points within this rectangle') and nearest-neighbor queries. Other spatial indexing approaches include grid files and quadtrees, which partition space into a grid or hierarchically subdivided regions.

6.5 Indexing for Search
Beyond exact-match and range queries on structured attributes, databases often need to support search over text and other complex data:
•	Full-text indexing: builds an inverted index — for each keyword (term), a list ('posting list') of the documents/records that contain that term (often with additional information such as term frequency or position) — allowing efficient retrieval of all documents containing a given word or combination of words.
•	Ranking of results: full-text/keyword search typically also involves ranking matching documents by relevance, commonly using statistical measures based on term frequency (how often a term appears in a document) and inverse document frequency (how rare the term is across the collection), so that more relevant documents are returned first.
•	Such indexing techniques underlie search functionality in document/text databases (mentioned in Unit I) and are increasingly integrated into general-purpose database systems alongside traditional indices.

6.6 Query Processing and Optimization
Query processing refers to the steps involved in extracting data from a database — given a declarative SQL query, the system must determine an efficient way to execute it. The broad steps are: (1) parsing and translation of the query into an internal form (such as an extended relational-algebra expression), (2) optimization — finding an efficient evaluation plan, and (3) evaluation — executing the chosen plan against the database to produce the result.

6.6.1 Measures of Query Cost
The cost of a query-evaluation plan is typically dominated by the cost of accessing data from disk, since disk access is far slower than memory access or CPU computation. Common cost measures and considerations include:
•	Number of block transfers between disk and main memory, and number of disk seeks — these are usually the dominant factors and are often used as the primary measure of cost (sometimes combined into a weighted formula with CPU cost).
•	The cost of writing output back to disk (if the result must be materialized) is also considered, in addition to the cost of reading the input relations.
•	Cost estimates are based on statistics maintained by the database system about relations, such as the number of tuples, number of distinct values for an attribute, and the size of the relation — these statistics are used by the query optimizer to estimate the cost of different plans without actually executing them.

6.6.2 Evaluation of Expression
Once a query has been translated into an expression in an algebra (such as relational algebra), there are generally two approaches to evaluating the expression:
•	Materialization: evaluate the expression from the bottom up, storing (materializing) the result of each intermediate operation as a temporary relation on disk, which is then used as input to the next operation.
•	Pipelining: evaluate several operations simultaneously, passing the result of one operation directly to the next operation as it is produced, without storing the intermediate result to disk — pipelining can significantly reduce the cost of writing/reading intermediate results, but is not always possible (some operations, such as sorting, may need to see their entire input before producing any output).

6.6.3 Choice of Evaluation Plans
For any given query, there may be many different relational-algebra expressions (and, for each expression, many different ways to evaluate each operation) that compute the same result. The query optimizer must choose one of these equivalent plans:
•	Generation of query-evaluation plans: an evaluation plan defines exactly what algorithm to use for each operation, and how the execution of the operations should be coordinated (e.g., which join algorithm, in what order to perform joins, and whether to materialize or pipeline).
•	Equivalent expressions: relational-algebra equivalence rules (such as the commutativity and associativity of join, and pushing selections and projections as early as possible) allow the optimizer to transform an initial query expression into many equivalent expressions, some of which may be much cheaper to evaluate.
•	Cost-based optimization: the optimizer estimates the cost of each candidate plan (using the cost measures from 6.6.1 and statistics from the system catalog) and chooses the plan with the lowest estimated cost. Because the space of possible plans can be very large, optimizers typically use dynamic programming or heuristic techniques (e.g., considering only 'left-deep' join trees) to limit the plans actually considered.
•	The end goal of query optimization is to minimize resource usage (and hence response time) while still producing a result that is logically equivalent to the result of the original declarative query — this is the key benefit of using a declarative query language like SQL, since the same query can be evaluated efficiently regardless of how it was originally phrased.
