### CMSC424 Reading Notes
#### Kaitlyn Yang | February 13, 2021

## Chapter 8: Relational Database Design

- there exists a *normal form* for schemas which gets rid of unnecessary redundancy and retrieving information easily
- topics: functional dependencies, normal forms

### 8.1 Features of Good Relational Designs

- there are a lot of issues when creating relations
- functional dependency are a type of rule that specifies such relations
- functional dependencies and other observations/rules may note where a schema should be decomposed (split) into two or more schemas
- however not all decomposition is helpful: may result in information loss with two types: lossy decompositions, lossless decompositions

### 8.2 Atomic Domains and First Normal Form

- Domain is `atomic` if elements of the domain are considered to be indivisible
    - hence breaking composite attributes into multiple
- Relation schema *R* is in `first normal form (1NF)` if the domains of *all* attributes are atomic

### 8.3 Decomposition Using Functional Dependencies

for the following chapter

**key**

- Greek letters for sets of attributes, lowercase when referring to a set that may or may not be a schema
- superkey is denoted by *K*
- lowercase name for relations

**Keys and Functional Dependencies**

- `legal instance`: when all instances of a relation satisfies real world constraints

let *r(R)* be a relation schema, and *K* be the superkey; *t<sub>1</sub>* and *t<sub>2</sub>* are tuples. let &alpha; &subset; *R* and &beta; &subset; *R*
- `Functional Dependency`: &alpha; -> &beta; if all pairs of tuples in the instance *t<sub>1</sub>*[&alpha;] = *t<sub>2</sub>*[&alpha;] then *t<sub>1</sub>*[&beta;] = *t<sub>2</sub>*[&beta;] ; holds on a schema if every legal instance of *r(R)* satisfies functional dependency
    - used to test instances of relations
    - to specify constraints for a set of legal relations
    - they are `trivial` if they are satisfied by all relations (i.e A->A or AB->A); &alpha;->&beta; is generally trivial if &beta;&subset;&alpha;
- it is also possible to infer other functional dependencies that hold i.e. *r(A,B,C)* *A->B* and *B->C* then *A->C*
- *F<sup>+</sup>* denote `closure` of set F, set of all functional dependencies that can be inferred given set F

**Boyce-Codd Normal Form**

- `Boyce-Codd normal form (BCNF)` is a desirable normal form
- eliminates all redundancy that can be discovered based on functional dependencies
- a relation schema *R* is in BCNF with respect to set *F* of functional dependencies if for all functional dependencies in *F<sup>+</sup>* of form &alpha;->&beta; if at least one of the following holds:
    - &alpha;->&beta; is a trivial functional dependency (&beta;&subset;&alpha;) or
    - &alpha; is a superkey for schema *R*
- a database design is in BCNF if each member of the set of relation schemas is in BCNF

**Decomposing from BCNF**

Let *R* be a schema not in BCNF, then there is at least one nontrivial functional dependency &alpha;->&beta; then replace *R* with the two schemas:
- (&alpha; &cup; &beta;)
- (*R* &minus; (&beta; &minus; &alpha;))

*skipped a few sections here*

### 8.4 Functional-Dependency Theory

**Closure of a Set of Functional Dependencies**

Given a relational schema *r(R)*, a functional dependency *f* on *R* is logically implied by a set of functional dependencies *F* on *r* if every instance of *r(R)* that satisfies *F* also satisfies *f*

ex.
let *r(A,B,C,G,H,I)* and *F*:

- A->B
- A->C
- CG->H
- CG->I
- B->H

here A->H is logically implied. 

- closure of *F* is denoted *F<sup>+</sup>*
- `Armstrong's Axioms`:
    - Reflexivity rule: if &alpha; is a set of attributes and &beta;&subset;&alpha; then &alpha;->&beta; holds
    - Augmentation rule: if &alpha;->&beta; holds and y is a set of attributes then y&alpha;->y&beta; holds
    - Transivity rule: if &alpha;->&beta; holds and &beta;->y holds then &alpha;->y holds

- Additional rules:
    - Union rule: if &alpha;->&beta; holds and &alpha;->y holds then &alpha;->&beta;y holds
    - Decomposition rule: if &alpha;->&beta;y holds then &alpha;->&beta; holds and &alpha;->y holds
    - Pseudotransivity rule: if &alpha;->&beta; holds and y&beta;->s holds then &alpha;y->s holds

Given the same relation as the one above, then on top of A->H, the following hold:

- CG->HI since CG->H and CG->I (union)
- AG->I since A->C and CG->I (pseudotransivity)

**Closure of Attribute Sets**

- attribute *B* is `functionally determined` by &alpha; if &alpha;->*B* ; in other words, &alpha; is a superkey
- this is closure under &alpha;, the set of attributes that is closed is denoted &alpha;<sup>+</sup>

**Canonical Cover**

- an attribute is extraneous if we can remove it without changing the closure of the set of functional dependencies