# TP4 Repport

- Larry ANDRIAMAMPIANINA
- Neil Toffa

## 1 Hive Beeline Client

### 1.1 Create a Connection to beeline Client

**Connect to the Hive**

> beeline -u 'jdbc:hive2://hadoop-master01.efrei.online:2181,hadoop-master02.efrei.online:2181,hadoop-master03.efrei.online:2181/;serviceDiscoveryMode=zooKeeper;zooKeeperNamespace=hiveserver2'

![Images](https://github.com/larryandria/Big-Data-Framework-II/blob/main/TP4/Images/1.png?raw=true)

**Type help command for list of beeline commands**

> help

![Images](https://github.com/larryandria/Big-Data-Framework-II/blob/main/TP4/Images/2.png?raw=true)

**Which command allows you to view the jdbc connection used to connect to HiveServer2?**


**List all databases**

> show databases;

**Create database**

>create database landriamampianina;

![Images](https://github.com/larryandria/Big-Data-Framework-II/blob/main/TP4/Images/3.png?raw=true)

**Use database**

> use landriamampianina;

![Images](https://github.com/larryandria/Big-Data-Framework-II/blob/main/TP4/Images/4.png?raw=true)

**List the tables**

> show tables;

![Images](https://github.com/larryandria/Big-Data-Framework-II/blob/main/TP4/Images/5.png?raw=true)

**Create table temp**

> create table temp (col STRING);

![Images](https://github.com/larryandria/Big-Data-Framework-II/blob/main/TP4/Images/6.png?raw=true)

> show tables;

![Images](https://github.com/larryandria/Big-Data-Framework-II/blob/main/TP4/Images/7.png?raw=true)

**List the columns of temp**

> describe temp;

![Images](https://github.com/larryandria/Big-Data-Framework-II/blob/main/TP4/Images/8.png?raw=true)

**Remove temp**

> drop table temp;

![Images](https://github.com/larryandria/Big-Data-Framework-II/blob/main/TP4/Images/9.png?raw=true)


## 1.2 Create tables

**Create an external table**

> CREATE EXTERNAL TABLE IF NOT EXISTS trees_external (
GEOPOINT STRING, ARRONDISSEMENT INT, GENRE STRING, ESPECE STRING, Famille STRING, ANNEE_PLANTATION INT,
HAUTEUR FLOAT, CIRCONFERENCE FLOAT, ADRESSE STRING, NOM_COMMUN STRING, VARIETE STRING, OBJECTID INT, NOM_EV STRING)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ';'
STORED AS TEXTFILE
LOCATION '/user/landriamampianina/trees';

![Images](https://github.com/larryandria/Big-Data-Framework-II/blob/main/TP4/Images/10.png?raw=true)

**Create an internal table**

> CREATE TABLE IF NOT EXISTS trees_internal (
GEOPOINT STRING, ARRONDISSEMENT INT, GENRE STRING, ESPECE STRING, Famille STRING, ANNEE_PLANTATION INT,
HAUTEUR FLOAT, CIRCONFERENCE FLOAT, ADRESSE STRING, NOM_COMMUN STRING, VARIETE STRING, OBJECTID INT, NOM_EV STRING)
STORED AS ORC;

![Images](https://github.com/larryandria/Big-Data-Framework-II/blob/main/TP4/Images/11.png?raw=true)


**Import data to the internal table using the external table**

> INSERT OVERWRITE TABLE trees_internal SELECT * FROM trees_external;

![Images](https://github.com/larryandria/Big-Data-Framework-II/blob/main/TP4/Images/12.png?raw=true)


**Verify that each table got the same lines count**

> SELECT COUNT(*) FROM trees_external;

![Images](https://github.com/larryandria/Big-Data-Framework-II/blob/main/TP4/Images/13.png?raw=true)

> SELECT COUNT(*) FROM trees_internal;

![Images](https://github.com/larryandria/Big-Data-Framework-II/blob/main/TP4/Images/14.png?raw=true)

### 1.3 Create queries

**displays the list of distinct containing trees**

> SELECT DISTINCT ARRONDISSEMENT FROM trees_internal;

![Images](https://github.com/larryandria/Big-Data-Framework-II/blob/main/TP4/Images/15.png?raw=true)

**displays the list of different species trees**

> SELECT DISTINCT ESPECE FROM trees_internal;

![Images](https://github.com/larryandria/Big-Data-Framework-II/blob/main/TP4/Images/16.png?raw=true)

**the number of trees of each kind**

> SELECT ESPECE, COUNT(ESPECE) FROM trees_internal GROUP BY ESPECE;

![Images](https://github.com/larryandria/Big-Data-Framework-II/blob/main/TP4/Images/17.png?raw=true)

**the height of the tallest tree of each king**

> SELECT ESPECE, MAX(HAUTEUR) FROM trees_internal GROUP BY ESPECE;

![Images](https://github.com/larryandria/Big-Data-Framework-II/blob/main/TP4/Images/18.png?raw=true)

**sort the trees height from smallest to larget**

> SELECT GEOPOINT, ARRONDISSEMENT, GENRE, ESPECE, HAUTEUR FROM trees_internal SORT BY HAUTEUR ASC;

![Images](https://github.com/larryandria/Big-Data-Framework-II/blob/main/TP4/Images/19.png?raw=true)

**displays the district where the oldest tree is**

> SELECT arrondissement, annee_plantation FROM trees_internal JOIN (SELECT MIN(annee_plantation) as min_annee FROM trees_internal) trees ON annee_plantation = trees.min_annee;

![Images](https://github.com/larryandria/Big-Data-Framework-II/blob/main/TP4/Images/20.png?raw=true)

**displays the district that contains the most trees**

> SELECT arrondissement, number_trees FROM (SELECT arrondissement, count(arrondissement) as number_trees FROM trees_internal GROUP BY arrondissement) tmp1, (SELECT MAX(number_trees) as max_number_trees FROM (SELECT arrondissement, count(arrondissement) as number_trees FROM trees_internal GROUP BY arrondissement) tmp2) trees Where tmp1.number_trees = trees.max_number_trees;

![Images](https://github.com/larryandria/Big-Data-Framework-II/blob/main/TP4/Images/21.png?raw=true)


