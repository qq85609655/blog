---
title: spark-R纪要
date: 2017-06-09 11:31:12
tags: 
    - spark
    - R
    - learn
toc: true
---

[TOC]

## R资源
### 主页
- http://www.r-project.com

### CRAN comprehensive R archive network

### blog
- http://www.r-bloggers.com

### books
- 数据挖掘与R语言
- R语言实战
- R语言编程艺术

### 观点
- R成为统计、预测分析和数据可视化的全球通用语言


## 环境准备
### 编译spark
1. 准备源码：$SPARK_SRC_HOME=
1. 执行 
    ``` shell
        cd $SPARK_SRC_HOME
        mvn -Pyarn -Phive -Phive-thriftserver -Psparkr -Pbigtop-dist -Phadoop-2.6 -Dhadoop.version=2.6.0 -DskipTests clean package -X -e
    ```
2. 得到：
    ll $SPARK_SRC_HOME/R
    ``` shell
    -rwxr-xr-x 1 elasticsearch elasticsearch 3256 Dec 16 10:02 check-cran.sh
    -rw-r--r-- 1 elasticsearch elasticsearch 3232 Dec 16 10:02 CRAN_RELEASE.md
    -rwxr-xr-x 1 elasticsearch elasticsearch 1761 Dec 16 10:02 create-docs.sh
    -rw-r--r-- 1 elasticsearch elasticsearch  474 Dec 16 10:02 DOCUMENTATION.md
    -rw-r--r-- 1 elasticsearch elasticsearch 1175 Dec 16 10:02 install-dev.bat
    -rwxr-xr-x 1 elasticsearch elasticsearch 2136 Dec 16 10:02 install-dev.sh
    drwxr-xr-x 3 root          root          4096 Jun  8 16:56 lib
    -rw-r--r-- 1 elasticsearch elasticsearch 1300 Dec 16 10:02 log4j.properties
    drwxr-xr-x 7 elasticsearch elasticsearch 4096 Dec 16 10:02 pkg
    -rw-r--r-- 1 elasticsearch elasticsearch 3735 Dec 16 10:02 README.md
    -rwxr-xr-x 1 elasticsearch elasticsearch 2311 Dec 16 10:02 run-tests.sh
    -rw-r--r-- 1 elasticsearch elasticsearch 2291 Dec 16 10:02 WINDOWS.md
    ```
3. 测试代码：$SPARK_HOME/examples/src/main/r/dataframe.R
``` r
library(SparkR)

# Initialize SparkSession
sparkR.session(appName = "SparkR-DataFrame-example")

# Create a simple local data.frame
localDF <- data.frame(name=c("John", "Smith", "Sarah"), age=c(19, 23, 18))

# Convert local data frame to a SparkDataFrame
df <- createDataFrame(localDF)

# Print its schema
printSchema(df)
# root
#  |-- name: string (nullable = true)
#  |-- age: double (nullable = true)

# 已做更改，Create a DataFrame from a JSON file
path <- "/dev/datacenter/input/r/people.json"

peopleDF <- read.json(path)
printSchema(peopleDF)
# root
#  |-- age: long (nullable = true)
#  |-- name: string (nullable = true)

# Register this DataFrame as a table.
createOrReplaceTempView(peopleDF, "people")

# SQL statements can be run by using the sql methods
teenagers <- sql("SELECT name FROM people WHERE age >= 13 AND age <= 19")

# Call collect to get a local data.frame
teenagersLocalDF <- collect(teenagers)

# Print the teenagers in our dataset
print(teenagersLocalDF)

# Stop the SparkSession now
sparkR.session.stop()

```

4. 开发案例功能测试：
    1. 准备hdfs数据文件：
    ``` shell
    hadoop dfs -put $SPARK_HOME/examples/src/main/resources/people.json /dev/datacenter/input/r
    ```
    2. 数据文件：people.json
    ``` json
    {"name":"Michael"}
    {"name":"Andy", "age":30}
    {"name":"Justin", "age":19}
    ```
    3. 运行
    ``` shell
    spark-submit ./examples/src/main/r/dataframe.R --master yarn-client
    ```
    4. 结果：
    ``` shell
    2017-06-15 17:15:07,567 INFO  [nioEventLoopGroup-2-2] codegen.CodeGenerator: Code generated in 12.78201 ms
        name
    1 Justin    
    ```


## 实践
### R加载mysql数据
#### 环境准备
- $install.packages("RMySQL") 安装RMySQL 包\
- $library(RMySQL) 
- $help(package="RMySQL")
- $con <- dbConnect(MySQL(),host="host",dbname="dbname",user="root",password="passwd")
- $summary(con)
- $dbGetInfo(con)
- $dbListTables(con)
- $dbRemoveTable(con,"test") 
- $dbDisconnect(con) #断开连接

``` r
    res <- dbGetQuery(con, "SELECT count, missing_count, unique_count FROM stats_general;")
    plot(res$unique_count, res$missing_count, xlab="unique()", ylab="missing()", main="graph_unique_missing")
    plot(res$count, res$missing_count, xlab="count()", ylab="missing()", main="graph_count_missing")
    plot(res$count, res$unique_count, xlab="count()", ylab="unique()", main="graph_count_unique")
```

#### 

## 命令积累
``` r
install.packages("ggplot2")
install.packages("rmarkdown")
install.packages("xtable")
```

## 读书纪要

### R语言实战（R  in action）

#### R语言介绍


#### 创建数据集



#### 图形初阶



#### 基本数据管理



#### 高级数据管理



#### 基本图形




#### 基本统计分析



#### 回归



#### 方差分析


#### 功效分析


#### 中级绘图



#### 重抽样与自助法



#### 广义线性模型



#### 主成分和因子分析



#### 处理缺失数据的高级方法




#### 高级图形进阶



#### 附录



#### 探索R的世界
 


