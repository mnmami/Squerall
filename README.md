[![Gitter](https://img.shields.io/gitter/room/DAVFoundation/DAV-Contributors.svg?style=flat-square)](https://gitter.im/squerall)
[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.1247913.svg)](https://zenodo.org/record/1247913)

# Squerall (previously Sparkall)
An implementation of the so-called Semantic Data Lake, using Apache Spark and Presto. Semantic Data Lake is a Data Lake accessed using Semantic Web technologies: ontologies and query language (SPARQL).

Currently supported data sources:
- Evaluated: CSV, Parquet, MongoDB, Cassandra, JDBC (MySQL, SQL Server, etc.).
- Experimental: Elasticsearch, Couchbase.
You can extend Squerall to support more, see [below](#extensibility).

## Setup and Execution
*- Prerequisite:* You need Maven to build Squerall from the source. Refer to the official documentations for installation instructions: [Maven](https://maven.apache.org/install.html) and [SBT](https://www.scala-sbt.org/1.0/docs/Setup.html). Once that is installed, run:
```
git clone https://github.com/EIS-Bonn/squerall.git
cd squerall
mvn package
cd target
```
...by default, you find a *squerall-0.1.0.jar* file.

Squerall (previously Sparkall) uses Spark and Presto as query engine. User specifies which underlying query engine to use. Therefore Spark and/or Presto has to be installed beforehand. Both Spark and Presto are known to among the easiest frameworks to configure and get started with. You can choose to run Spark/Presto and thus Squerall in a single node, or deploy them in a cluster.

### Spark
- Download Spark from the [Spark official website](https://spark.apache.org/downloads.html). In order for Spark to run in a cluster, you need to configure a 'standalone cluster' following guidelines in the [official documentation page](https://spark.apache.org/docs/2.2.0/spark-standalone.html).

- Once Spark is installed, navigate to `bin` folder and run `spark-submit` script giving in arguments three files ---built using [Squerall-GUI](https://github.com/EIS-Bonn/squerall-gui) (see below).
The command line looks like:
`/bin/spark-submit --class [Main classpath] --master [master URI] --executor-memory [memory reserved to the app] [path to squerall-0.1.0.jar] [query file] [mappings file] [config file] [master URI] n s`

- #### Example:
`/bin/spark-submit --class org.squerall.Main --master spark://127.140.106.146:3077 --executor-memory 250G /etc/squerall-0.1.0.jar query.sparql mappings.ttl config spark://172.14.160.146:3077 n p`

  * query file: a file containing a correct SPARQL query, only.
  * mappings file: a file contains RML mappings linking data to ontology terms (classes and properties), in JSON format.
  * config file: a file containing information about how to access data sources (eg. host, user, password), in JSON format.


  ** - Known issue:** if the error `java.lang.NullPointerException at org.apache.jena.query.ARQ.isTrue(ARQ.java:650)` is returned from `mvn package`, it means that `jena arq` dependncy isn't correctly packaged. We are searching for a solution, but as a temporary workaround, download Jena ARQ jar file and add it to `spark-submit` commend by modifying the previous command as follows: `/bin/spark-submit --class ... --jars jena-arq-3.9.0.jar /etc/squerall-0.1.0.jar query.sparql ...` (same command but with `--jars jena-arq-3.9.0.jar` before `/etc/squerall-0.1.0.jar`).

### Presto
- Install Presto from [Presot official website](https://prestodb.io/docs/current/installation/deployment.html).
- Once Presto is installed, navigate to `bin` folder and run `squerall-0.1.0.jar` like you run any Java application:
`java -cp [path to squerall-0.1.0.jar] org.squerall.Main [query file] [mappings file] [config file] [Presto server url (host:port)] n p`

  * query, mappings and config files are identical to Spark command above.

- #### Example:
`java -cp /etc/squerall-0.1.0.jar org.squerall.Main query.sparql mappings.ttl config jdbc:presto://localhost:8080 n p`

  **- Note:** If any error raised due to Presto libs not found, download and append `presto-jdbc-{xyz}.jar` (e.g., from [here](http://central.maven.org/maven2/io/prestosql/presto-jdbc/304/presto-jdbc-304.jar
) for version 'presto-jdbc-304') "`:presto-jdbc-xyz.jar`" to `squerall-0.1.0.jar` in the command.

- #### Presto and Hive metastore
Presto is meant to access existing database management systems; therefore, it doesn't have its own metadata store. For file-based data sources, like CSV and Parquet, Presto uses Hive metastore. As a result, prior to running queries in Presto, CSV and Parque files have to be registered in Hive metastore. Parquet files can be registered using [Presto Hive connector (see 'Examples')](https://prestodb.io/docs/current/connector/hive.html); CSV files need to be registered inside Hive as an [*external* table (see 'Create an external table')](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.5/bk_data-access/content/moving_data_from_hdfs_to_hive_external_table_method.html).

## Squerall-GUI
Squerall has 3 interfaces to (1) provide access configuration to data in the Data Lake, (2) map data to ontology terms and (3) query the mapped data. These interfaces generate the needed input files used for query execution: config, mappings and query, respectively. Refer to Squerall-GUI repository here: [Squerall-GUI](https://github.com/EIS-Bonn/squerall-gui) for more information.

## Evaluation
We provide in this repository the code-source, queries and docker image for anyone who wants to try Squerall on their own. Refer to the [dedicated page](https://github.com/EIS-Bonn/Squerall/tree/master/evaluation).

## Extensibility
Squerall is extensible by design, developers can themselves add support to more data sources, or even add a new query engine alongside Spark and Presto. Refer to the [Wiki](https://github.com/EIS-Bonn/Squerall/wiki/Extending-Squerall) for the details.

## Publication
A preprint describing Squerall can be found at [Squerall: Virtual Ontology-Based Access to
Heterogeneous and Large Data Sources](http://www.semantic-web-journal.net/system/files/swj1957.pdf). The preprint details all the building blocks and reports on experiments conducted to demonstrate Squerall's capabilities.

## Contact
For any setup difficulties or other inquiries, please contact me on: mami@cs.uni-bonn.de, or ask directly on [Gitter chat](https://gitter.im/squerall).

License
-------

This project is openly shared under the terms of the __Apache License
v2.0__ ([read for more](./LICENSE)).
