以下为修改过的版本。

# node2vec on spark

This library is a implementation using scala for running on spark of *node2vec* as described in the paper:
> node2vec: Scalable Feature Learning for Networks.
> Aditya Grover and Jure Leskovec.
> Knowledge Discovery and Data Mining, 2016.
> <Insert paper link>

The *node2vec* algorithm learns continuous representations for nodes in any (un)directed, (un)weighted graph. Please check the [project page](https://snap.stanford.edu/node2vec/) for more details. 


### Building node2vec_spark
**In order to build node2vec_spark, use the following:**

```
$ mvn clean package
```

**and requires:**<br/>
Maven 3.0.5 or newer<br/>
Java 7+<br/>
Scala 2.10 or newer.

This will produce jar file in "node2vec_spark/target/"

### Examples
This library has two functions: *randomwalk* and *embedding*. <br/> 
These were described in these papers [node2vec: Scalable Feature Learning for Networks](http://arxiv.org/abs/1607.00653) and [Efficient Estimation of Word Representations in Vector Space](https://arxiv.org/abs/1301.3781).

### Random walk
Example:
	
    spark-submit --class com.navercorp.Main \
    ./target/node2vec-0.0.2-SNAPSHOT.jar \
    --cmd randomwalk \
    --input ./graph/karate_edgelist.txt --output ./result/karate \
    --weighted true --walkLength 10 --numWalks 10 --p 1.0 --q 1.0 --indexed true


* If "indexed" is set to false, *node2vec_spark* index nodes in input edgelist, example: <br/>
  **unindexed edgelist:**<br/>
  node1 	node2 	1.0<br/>
  node2 	node7 	1.0<br/>
  
  **indexed:**<br/>
  1 	2 	1.0<br/>
  2 	3 	1.0<br/>

  1 	node1<br/>
  2 	node2<br/>
  3 	node7

#### Input
The supported input format is an edgelist:

	node1_id_int 	node2_id_int 	<weight_float, optional>
	or
	node1_str 	node2_str 	<weight_float, optional>, Please set the option "indexed" to false


#### Output
The output file (number of nodes)*numWalks random paths as follows:

	src_node_id_int 	node1_id_int 	node2_id_int 	... 	noden_id_int


### Embedding random paths
Example:
	
    spark-submit --class com.navercorp.Main \
                      ./target/node2vec-0.0.2-SNAPSHOT.jar \
                      --input ./result/karate.path --output ./result/karate \
                      --cmd embedding --dim 5 --iter 1


#### Input
The supported input format is an random paths:

	src_node_id_int 	node1_id_int 	... 	noden_id_int

#### Output
The output files are **embeddings and word2vec model.** The embeddings file has the following format: 

	node1_str 	dim1 dim2 ... dimd

where dim1, ... , dimd is the d-dimensional representation learned by word2vec.

the output file *word2vec model* has the spark word2vec model format. please reference to https://spark.apache.org/docs/1.5.2/mllib-feature-extraction.html#word2vec

## References
1. [node2vec: Scalable Feature Learning for Networks](http://arxiv.org/abs/1607.00653)
2. [Efficient Estimation of Word Representations in Vector Space](https://arxiv.org/abs/1301.3781)