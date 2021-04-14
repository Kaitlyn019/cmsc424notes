### CMSC424 Notes
#### Kaitlyn Yang | 03/09/2021


#### SPARKS

**aggregateByKey(a_tuple, within_reduction, cross_partition)**
*where*
within_reduction(tuple, scalar) -> tuple
    - lambda a,b: ~~~
    - a is the already existing tuple (a_tuple)
    - b is the next value
cross_partition(tuple, tuple) -> tuple
    - lambda a,b: ~~~
    - a is the NEW tuple created from within_reduction
    - b is the next tuple
ref: https://stackoverflow.com/questions/29930110/calculating-the-averages-for-each-key-in-a-pairwise-k-v-rdd-in-spark-with-pyth
