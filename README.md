
# Rows
* Rows represent data that fits into the columns and types of the `DataFrame`
* Rows themselves do not have schemas
* If you create a Row manually, you must specify the values in the same order as the schema of the DataFrame



```scala
import org.apache.spark.sql.types._
import org.apache.spark.sql.Row

val structType = new StructType(Array(
    StructField("id", IntegerType, false),
    StructField("firstName", StringType, false),
    StructField("lastName", StringType, false),
    StructField("department", StringType, false)))

val data = Seq(
     Row(1, "James", "Gosling", "Coffee and Accessories"),
     Row(2, "Guido", "Van Rossum", "Snakes, Lizards, Spiders"),
     Row(3, "Bjarne", "Stroustrup", "Optometry"),
     Row(4, "John", "McCarthy", "Speech Therapy")
   )

val dataFrame = spark.createDataFrame(spark.sparkContext.parallelize(data), structType)
dataFrame.show()
```

    +---+---------+----------+--------------------+
    | id|firstName|  lastName|          department|
    +---+---------+----------+--------------------+
    |  1|    James|   Gosling|Coffee and Access...|
    |  2|    Guido|Van Rossum|Snakes, Lizards, ...|
    |  3|   Bjarne|Stroustrup|           Optometry|
    |  4|     John|  McCarthy|      Speech Therapy|
    +---+---------+----------+--------------------+
    
    




    import org.apache.spark.sql.types._
    import org.apache.spark.sql.Row
    structType: org.apache.spark.sql.types.StructType = StructType(StructField(id,IntegerType,false), StructField(firstName,StringType,false), StructField(lastName,StringType,false), StructField(department,StringType,false))
    data: Seq[org.apache.spark.sql.Row] = List([1,James,Gosling,Coffee and Accessories], [2,Guido,Van Rossum,Snakes, Lizards, Spiders], [3,Bjarne,Stroustrup,Optometry], [4,John,McCarthy,Speech Therapy])
    dataFrame: org.apache.spark.sql.DataFrame = [id: int, firstName: string ... 2 more fields]
    



## Adding a row or rows

* `union` can add one or more rows together
* `union` requires a `DataSet` of `Row`


```scala
val matz = (5, "Yukihiro", "Matsumoto", "Jewelry")
val wall = (6, "Larry", "Wall", "Jewelry")
val jewelers = Seq(matz, wall).toDF("id", "firstName", "lastName", "department")
```




    matz: (Int, String, String, String) = (5,Yukihiro,Matsumoto,Jewelry)
    wall: (Int, String, String, String) = (6,Larry,Wall,Jewelry)
    jewelers: org.apache.spark.sql.DataFrame = [id: int, firstName: string ... 2 more fields]
    



## Combining Rows using `union` 


```scala
val combinedDF = dataFrame.union(jewelers)
combinedDF.show()
```

    +---+---------+----------+--------------------+
    | id|firstName|  lastName|          department|
    +---+---------+----------+--------------------+
    |  1|    James|   Gosling|Coffee and Access...|
    |  2|    Guido|Van Rossum|Snakes, Lizards, ...|
    |  3|   Bjarne|Stroustrup|           Optometry|
    |  4|     John|  McCarthy|      Speech Therapy|
    |  5| Yukihiro| Matsumoto|             Jewelry|
    |  6|    Larry|      Wall|             Jewelry|
    +---+---------+----------+--------------------+
    
    




    combinedDF: org.apache.spark.sql.Dataset[org.apache.spark.sql.Row] = [id: int, firstName: string ... 2 more fields]
    



## Get the first row


```scala
val row = combinedDF.head()
```




    row: org.apache.spark.sql.Row = [1,James,Gosling,Coffee and Accessories]
    



## Take the first few rows


```scala
val array = combinedDF.take(3)
array.mkString
```




    array: Array[org.apache.spark.sql.Row] = Array([1,James,Gosling,Coffee and Accessories], [2,Guido,Van Rossum,Snakes, Lizards, Spiders], [3,Bjarne,Stroustrup,Optometry])
    res8: String = [1,James,Gosling,Coffee and Accessories][2,Guido,Van Rossum,Snakes, Lizards, Spiders][3,Bjarne,Stroustrup,Optometry]
    



## Get Elements of a row


```scala
val odersky = Row(7, "Martin", "Odersky", "Stair Repair")
println(odersky.getString(3))
```

    Stair Repair
    




    odersky: org.apache.spark.sql.Row = [7,Martin,Odersky,Stair Repair]
    




```scala
val kernighan = Row(7, "Brian", "Kernighan", "Oceanic Travel")
println(kernighan.getAs[Int](0))
```

    7
    




    kernighan: org.apache.spark.sql.Row = [7,Brian,Kernighan,Oceanic Travel]
    




```scala
val timBernersLee = Row(8, "Tim", "Berners Lee", "Spiders")
println(timBernersLee.apply(3))
println(timBernersLee(3))
```

    Spiders
    Spiders
    




    timBernersLee: org.apache.spark.sql.Row = [8,Tim,Berners Lee,Spiders]
    



## There is no direct access to a row using an index

* Spark is scaled across multiple nodes
* Gaining a direct access to a row would be ineffective
* Using generalized manipulation via `DataFrame` and functional programming would be how one would evoke change

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
