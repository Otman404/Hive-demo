# DEMO

- Docker Hive setup
- Process and manipulate HDFS Data via Hive



### HIVE Setup

1. Running Docker image

```bash
git clone https://github.com/big-data-europe/docker-hive
cd docker-hive
docker-compose up -d
```

The image contains:

- Namenode
- datanode
- Hive Server
- Hive metastore configured with PostgreSQL
- ..

After setting it up, we can access Hive and namenode servers:

```bash

#list running images
docker ps

# access Hive serve via bash terminal
docker exec -it docker-hive_hive-server_1 bash

# access Namenode serve via bash terminal ( so we can add some files to HDFS ...)
docker exec -it docker-hive_namenode_1 bash
```


2. Creating database/tables in Hive

```bash
# creating a database
create database bdcc_demo;

# list databases
show databases;

# use the database 'bdcc_demo' that we've just created
use bdcc_demo;

# creating 'students' table
create table students(firstname String, lastname String, age INT) row format delimited fields terminated by ',' stored as textfile;

# show 'students' table information
describe students;


# add some data to HDFS ( following the 'students' table schema)
hdfs dfs -mkdir -p /user/data

# create a text file
touch data.txt

# opening it in Vim to add some data in it. ex: john,doe,20
vim data.txt

# copy the file from local filesystem to HDFS
hdfs dfs -put data.txt /user/data


# Import Data to Hive

# METHOD 1: this will move our data to Hive warehouse

LOAD DATA INPATH '/user/data/data.txt' OVERWRITE INTO TABLE students;


# show 'students' table data;
select * from students;

# METHOD 2: This will keep data in its original path without moving it. and Hive will read it from that path.
# creating external table
create external table students2(nom String, prenom String, age INT) row format delimited fields terminated by ',' LOCATION '/user/data';
```