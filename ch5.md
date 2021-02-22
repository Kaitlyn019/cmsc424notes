### CMSC424 Reading Notes
#### Kaitlyn Yang | February 13, 2021


## Chapter 5 : Advanced SQL

>**topics**:
>- SQL from a programming language
>- functions & procedural code
>- triggers
>- recursive queries
>- advanced aggregation features
>- OLAP systems

### 5.2 Functions and Procedures

**Declaring & Invoking**

- function definition is different depending on the syntax
- concept is the same as any other programming languages

PSQL syntax, example:
``` SQL
create function county_count(sc character(2))
returns bigint
as 'select count(*) from counties where counties.statecode=$1'
language sql;
```
generalized [^1]
``` SQL
create [or replace] function function_name (arg_list)
[ returns return_type ]
{ LANGUAGE lang_name
    as 'defintion'
} ... ;
```
\* for psql, overloading is legal

- table functions => functions that return tables as results, can be thought of as parametrerized views (generalized concept of views by allowing parameters) ex. can be used in the *from* statement
``` SQL 
select * 
from table(instructors_of('Finance'))
```
```SQL
create function instructors_of (dept_name varchar(20))
    returns table (
        ID varchar(5),
        name varchar(20),
        dept_name varchar(20),
        salary numeric(8,2))
return table
    (select ID, name, dept_name, salary
    from instructor
    where instructor.dept_name = instructor_of.dept_name)
```
generalized, format *for sql* is:
``` SQL
create function function_name (param_list)
    returns return_type as
    declare
    -- variable declaration
    begin
    -- logic
    end;
```
PSQL example:
``` SQL
create function get_county(sc character(30))
returns table (name character(30))
as 'select name from counties where counties.statecode=$1'
language sql;
```
- invoked in a query
``` SQL
select * from table(instructor_of('Finance'))
```

**Procedures**
- can rewrite many functions as procedures
example,
``` SQL
create procedure dept_count_proc(in dept_name varchar(20) out d_count integer)
    begin
        select count(*) into d_count
        from instructor
        where instructor.dept_name=dept_count_proc.dept_name
    end
```
- in = parameters for input, out = parameters whose values are set to return results
- invoked with call statement
``` SQL
declare d_count ingeter;
call dept_count_proc('Physics', d_count);
```
\* note psql version being used does not support procedures

**Language Constructs**
- supports while loops, for loops, if-then-else
- these can be used in functions and procedures
- supports exception conditions & declare handlers to handle the exceptions


### 5.3 Triggers

- trigger => statement that executes automatically as a side effect of a *modification* to the database
- design must meet two requirements:
    1. specify when a trigger is to be executed - an event that causes it to be checked, condition to be satisfied for trigger exec to proceed
    2. specify actions to be taken when trigger executes

generalized syntax,
``` SQL
create trigger trigger_name
on table_name
for | after | instead of {[insert],[update],[delete]} -- can choose more than one
[with append]
[not for replication]
as
{sql_statements}
```
\* changes? syntax in book not the same as google...

**issues with triggers**
- sometimes not the most effective, esp with options like on delete cascade for foregin-key constraints
- no need for trigger code for materialized views
- unintended execution


### 5.4 Recursive Queries

take a relation *prereq* for example (a relation that has the prereqs for each class); **transitive closure** of the relation is a relation that contains all the pairs (course_id, pre_id) such that all *pre_id* is a direct or indirect prerequisite of course_id.

other applications: hierarchies, machines with subparts, etc.

**Transitive Closure using Iteration**

- can use a `repeat` loop to handle interation
- `except` clause ensures the function works on edge cases

**Recursion in SQL**

- iteration is inconvenient, "recursive view definitions" more effective
- this is done by creating a temporary view is expressed in terms of itself, using `with recursive`

- to do this: define a recursive view as a union of two subqueries => base query (non-recursive) and recursive query (uses recursive view)

*example in SQL*
let the `prereq` relation be:

| course_id | prereq_id |
| --------- | --------- |
bio-200 | bio-100
bio-300 | bio-200
cs-190 | cs-101
cs-315 |cs-101
cs-319 | cs-190

``` SQL
with recursive c_prereq(course_id, prereq_id) as (
        -- base query
        select course_id, prereq_id
        from prereq
    union
        -- recursive query
        select prereq.prereq_id, c_prereq.course_id
        from prereq, c_prereq -- cartesian product
        where prereq.course_id = c_prereq.prereq_id
)
select *
from c_prereq;
```

*tracing the c_prereq*
0.

| course_id | prereq_id |
| --------- | ---------|

^ empty list

1. base query returns prereq itself, recursive query returns nothing = the union of these is just prereq

    | course_id | prereq_id |
    | --------- | --------- |
    bio-200 | bio-100
    bio-300 | bio-200
    cs-190 | cs-101
    cs-315 |cs-101
    cs-319 | cs-190

2. base query returns prereq itself again, recursive query 
looks for all courses in prereq where the course_id is equal to another courses prereq_id => adds that course_id to with the prereq_id

    (c_prereq's course_id, prereq's prereq_id)

    | course_id | prereq_id |
    | --------- | --------- |
    bio-300 | bio-100
    cs-319 | cs-101

    union with prereq to set c_prereq as:
    | course_id | prereq_id |
    | --------- | --------- |
    bio-200 | bio-100
    bio-300 | bio-200
    cs-190 | cs-101
    cs-315 |cs-101
    cs-319 | cs-190
    bio-300 | bio-100
    cs-319 | cs-101

3. base query returns prereq itself, recursive query once again looks for any cases where there are repeats, note: it is a cartesian product of the c_prereq in step 2 with pre_req (not c_prereq with c_prereq!)

    | course_id | prereq_id |
    | --------- | --------- |
    bio-300 | bio-100
    bio-200 | bio-100
    cs-319 | cs-101
    cs-190 | cs-101

    final c_prereq (nothing added):
    | course_id | prereq_id |
    | --------- | --------- |
    bio-200 | bio-100
    bio-300 | bio-200
    cs-190 | cs-101
    cs-315 |cs-101
    cs-319 | cs-190
    bio-200 | bio-300
    cs-190 | cs-319

This works as follows:
*at the start, prereq is the list of courses and c_prereq is empty*
1. Compute the base query, add result tuples to recursively defined view relation (added to c_prereq)
2. compute the recursive query using the current contents of the view relation
3. repeat step 2 until it returns no new tuples, reaching the fixed point

**restrictions**
- query should be monotonic: result on view relation V1 should be a superset of its result on a view relation V2 if V1 is a superset of V2
- i.e. with every single return, the result should include the result from the previous recurse and more if applicable (step 3's recursive query returned the values from step 2)

This means it should not do the following:
- Aggregation on the recursive view
- not exists on a subquery that uses the recursive view
- set difference (except) whose right hand side is the recursive view
    - example: r-v, where v is the recursive view, because v is growing with each turn this result becomes smaller

### 5.1 Accessing SQL from a Programming Language
(at the bottom because did not cover in class, not sure how important it is)

*why?*
1. Not all queries can be expressed in SQL (is not as powerful as a general-purpose languge)
2. nondeclarative actions i.e. printing, interacting with user, sending results to GUI, etc. - can not be done with SQL

**Approaches in Programming Languages**

- Dynamic SQL: allows a program to construct an SQL query as a character string at *runtime*, submit, and retrieve results as a tuple (ex. JDBC/Java, ODBC/C)

- Embedded SQL: SQL statements are identified at *compile time* with a preprocessor, submits the SQL statements to database for precompilation & optimization - replaces with appropriate code + function calls then invokes it

difficult to integrate bc SQL uses relations but programming languages work with variables which are the equivalent to an attribute

integrating requires the result of the query to change depending on the program

**JDBC** => defines an API (application program interface) that Java programs use to connect to database servers

 >general idea: connect to database, execute objects called "Statement"
 > - for database changes returns a int representing # of tuples changed
 > - for query results, returns an object "rset" with a set of tuples, using "next" and "get" can sift through relation

**ODBC** (Open Database Connectivity) => API for C, etc. 

*if we cover this in class, return to finish notes*

[^1]: https://www.postgresql.org/docs/9.1/sql-createfunction.html