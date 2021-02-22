### CMSC424 Reading Notes
#### Kaitlyn Yang | February 13, 2021


## Chapter 7 : Database Design and E-R Model

- entity-relationship data model (E-R) => identifying entities to be represented in the database, how entites are related
- how E-R design can be transformed into a set of relation schemas/how some constraints can be captured in design

### 7.1 Overview of the Design Process

**Design Phases**
1. Initial Phase: characterize the data fully for prospective database users, domains, outcomes, etc.
2. Conceptual Design Phase: detail overview of the enterprise, E-R diagram creation, create a specification of functional requirements
3. Logical Design Phase: high level conceptual schema, implementation data model maps conceptual schema defined by E-R to relation schema
4. Physical Design Phase: actually create the relation schema

**Design Considerations**
1. Redundancy: avoid repeating information because data can become inconsistent
2. Incompleteness: bad design might make it impossible to model

### 7.2 The Entity-Relationship Model
- the entity-relationship (E-R) maps meanings and interactions onto a conceptual schema.
- three basic concepts: entity sets, relationship sets, attributes

**Entity Sets**
- Entity is an object that is distinguishable from other objects because of a set of properties and unique values of some set identify the entity
- entity set is a set of entities of the same type that share the same properties or attributes
- extension refers to the collection of entities belonging to the entity set
- attributes descriptive properties of each member of an entity set, each with a value

**Relationship Sets**
- Relationship is an association among several entities
- Relationship set is a set of relationships of the same type; entity sets participate in a relationship set
- relationship instance is the assocation between the named entities in the real world that are being modeled
- number of entity sets that participate in a relationship set is the degree (two is binary, three is ternary)
- relationships may have descriptive attributes (i.e. when a instructor started teaching a student)

**Attributes**
- has a set of permitted values called domain/value set for attribute
- types:
    - simple attributes vs composite attributes (can divide into subparts)
    - single valued vs multivalued (may have more than one value for attribute)
    - derived attribute (can be derived from the values)

### 7.3 Constraints

**Mapping Cardinalities**
- cardinality ratios is the number of entities to which another entity can be associated via a relationship set
- for binary relationships:
    - one-to-one: entity in A associated with at most entity in B, vice versa
    - one-to-many: entity in A associated with zero or more entities in B, but B can only be associated with on eentity in A
    - many-to-one: above but switch A and B
    - many-to-many: entity in A associated with zero or more entities in B, vice versa
- participation constraints:
    - total: if every entity in E participates in at least one relationship (no nulls!)
    - partial: not total
- keys:
    - specify how entities are distinguished, same notion as relation keys

### 7.4 Removing Redundant Attributes in Entity Sets
Designing an E-R Model:
1. identify the entity sets that we need
2. choose appropriate attributes
3. for relationship sets
    - remove redundancies! 
    - a good entity-relationship design does not have redundant attributes

### 7.5 Entity-Relationship Diagrams

**Basic Structure**
- Rectangles divided into two parts represent entity sets
- Diamonds represent relationship sets
- Unidivided rectangles represent attributes of a relationship set, with primary keys underlined
- Lines link entity sets to relationship sets
- Dashed lines link attributes of a relationship set to a relationship set
- Double lines are total participation
- Double diamonds are a weak entity sets

**Cardinality**
- One is a directed line (arrow head), with arrow head pointing to the one of (i.e. A <- B means each B has one A but A does not necessarily have one B)
- Many is undirected line (no arrow head)
- Sometimes it is written as l..h where l is the minimum and h is the maximum cardinality and * means no limit

**Complex Attributes**
- composite attributes are broken up with tabs
- multivalued attributes written in {curly braces}
- derived attributes end with a ()

**Weak Entity Sets**
- weak entity set: an entity set without sufficient attributes to form a primary key
    - must be associated with another entity set; "identifying/owner entity set"
    - they are existence dependent on this set
    - relationship is an "identifying relationship", is many-to-one from weak entity set to identifying entity set
    - discriminator of the weak entity set can group entities in the set to link to an entity in the identifying set, aka a partial key
- strong entity set: opposite of above

### 7.6 Reduction to Relational Schema

**Strong Entity Sets with Simple Attributes**
Let E be a strong entity set with only simple descriptive attributes *a1, a2, ..., an* -> represented a schema called E with n distinct attributes. 
- primary keys in entity set are primary key in relation schema
- normal attributes become normal attributes

**Strong Entity Sets with Complex Attributes**
- composite attributes are separate attributes for each component
- multivalued attributes get new relation schemas
- derived attributes are not explicitly represented, usually with "methods"

**Representation of Weak Entity Sets**
Let *A* be a weak entity set with attributes *a1,a2,...an*. Let *B* be the strong entity set which A depends with primary key *b1,b2,...,bn*.
The relation schema is represented 

*{a1,a2,...,an} U {b1,b2,...,bn}*

where the primary key is the combination of the strong entity set primary key and the discriminator of the weak entity set.

- There is also a foreign-key constraint specifying *b1,b2,...,bn* reference primary key of *B*.

**Representation of Relationship Sets**
Let *R* be a weak entity set with attributes *a1,a2,...am* as the union of the primary keys of each entity set participating in *R*. Let *b1,b2,...,bn* be descriptive attributes of the relationship. The relation schema would be:

*{a1,a2,...,am} U {b1,b2,...,bn}*

- Also create foreign-key constraints from the entity sets to the relation schema

**Redundancy & Combinations of Schemas**
- start merging, remove sets that do not need to be present, consider many-to-one relationships


*return if there are any additional topics beyond what was necessary for quiz/class*

pg 287 to 340