
## Lab: Add Rows 

**Step 1:** Read from the `../data/students.csv` file

**Step 2:** Determine the count of data using `count()` on the `DataFrame`

**Step 3:** Take the following students, create a `DataFrame` from it, and add it to the `DataFrame` you just loaded

```
265-0983,95,93,96,86
613-3461,80,81,82,87
173-6835,81,91,85,72
146-4885,81,88,99,94
202-5972,86,97,93,80
619-2192,88,93,89,76
064-0847,91,82,81,74
684-5156,94,97,91,83
```

**Step 4:** Determine the count of the additional data

**Step 5:** Write the data to a file called `../data/mergedstudents.csv`

**Step 6:** Reread the file back in and determine if the `count` is still the same


```scala
val readDF = spark.read
     .format("csv")
     .option("inferSchema", "true")
     .option("header", "true")
     .load("../data/students.csv")
readDF.show(10)
```

    +----------+------------+-------------+----------+----------+
    |student_id|freshman_avg|sophomore_avg|junior_avg|senior_avg|
    +----------+------------+-------------+----------+----------+
    |  202-9509|          82|           90|        82|        82|
    |  645-9655|          91|           93|        99|        99|
    |  075-4491|          94|           84|        82|        78|
    |  191-7145|          95|           84|        90|        88|
    |  684-8946|          87|           90|        83|        86|
    |  243-7777|          94|           86|        83|        89|
    |  493-8040|          92|           95|        95|        71|
    |  314-1865|          89|           87|        88|        82|
    |  735-9127|          82|           98|        84|        99|
    |  279-9887|          98|           91|        79|        80|
    +----------+------------+-------------+----------+----------+
    only showing top 10 rows
    
    




    readDF: org.apache.spark.sql.DataFrame = [student_id: string, freshman_avg: int ... 3 more fields]
    




```scala
readDF.count
```




    res13: Long = 42
    




```scala
import org.apache.spark.sql.types.{StringType, IntegerType, StructType, StructField}
import org.apache.spark.sql.Row
val rows = Seq(
("265-0983",95,93,96,86),
("613-3461",80,81,82,87),
("173-6835",81,91,85,72),
("146-4885",81,88,99,94),
("202-5972",86,97,93,80),
("619-2192",88,93,89,76),
("064-0847",91,82,81,74),
("684-5156",94,97,91,83))

val additionDF = rows.toDF("student_id", "freshman_avg", "sophomore_avg", "junior_avg", "senior_avg")
```




    import org.apache.spark.sql.types.{StringType, IntegerType, StructType, StructField}
    import org.apache.spark.sql.Row
    rows: Seq[(String, Int, Int, Int, Int)] = List((265-0983,95,93,96,86), (613-3461,80,81,82,87), (173-6835,81,91,85,72), (146-4885,81,88,99,94), (202-5972,86,97,93,80), (619-2192,88,93,89,76), (064-0847,91,82,81,74), (684-5156,94,97,91,83))
    additionDF: org.apache.spark.sql.DataFrame = [student_id: string, freshman_avg: int ... 3 more fields]
    




```scala
additionDF.count()
```




    res14: Long = 8
    




```scala
val mergedDF = readDF.union(additionDF)
mergedDF.count()
```




    mergedDF: org.apache.spark.sql.Dataset[org.apache.spark.sql.Row] = [student_id: string, freshman_avg: int ... 3 more fields]
    res15: Long = 50
    




```scala
mergedDF.write
        .format("csv")
        .option("header", "true")
        .mode("overwrite")
        .save("../data/mergedstudents.csv")
```


```scala
val readMergedDF = spark.read
     .format("csv")
     .option("inferSchema", "true")
     .option("header", "true")
     .load("../data/mergedstudents.csv")

readMergedDF.count()
```




    readMergedDF: org.apache.spark.sql.DataFrame = [student_id: string, freshman_avg: int ... 3 more fields]
    res18: Long = 50
    


