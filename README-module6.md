## Question 1: Install Spark and PySpark

- Install Spark
- Run PySpark
- Create a local spark session
- Execute spark.version.

What's the output? <br>
> Ans: '4.1.1'

## Question 2: Yellow November 2025

Read the November 2025 Yellow into a Spark Dataframe.

Repartition the Dataframe to 4 partitions and save it to parquet.

What is the average size of the Parquet (ending with .parquet extension) Files that were created (in MB)? Select the answer which most closely matches.

- 6MB
- 25MB
- 75MB
- 100MB
  
> Ans: 25MB

## Question 3: Count records

How many taxi trips were there on the 15th of November?

Consider only trips that started on the 15th of November.

- 62,610
- 102,340
- 162,604
- 225,768

> Ans: 162,604
```
df.select('VendorID', 'tpep_pickup_datetime', 'tpep_dropoff_datetime').\
filter((df.tpep_pickup_datetime >= "2025-11-15") & (df.tpep_pickup_datetime < "2025-11-16")).count()
```
```
# another way
from pyspark.sql.functions import to_date, col

df.filter(
    to_date(col("tpep_pickup_datetime")) == "2025-11-15"
).count()
```
```
# spark sql way
df.createOrReplaceTempView("taxi_trips")

spark.sql("""
SELECT COUNT(*) AS trip_count
FROM taxi_trips
WHERE tpep_pickup_datetime >= '2025-11-15'
  AND tpep_pickup_datetime <  '2025-11-16'
""").show()
```

## Question 4: Longest trip

What is the length of the longest trip in the dataset in hours?

- 22.7
- 58.2
- 90.6
- 134.5

> Ans: 90.6
```
spark.sql("""
SELECT
    MAX(
        timestampdiff(SECOND,
                      tpep_pickup_datetime,
                      tpep_dropoff_datetime) / 3600.0
    ) AS longest_trip_hours
FROM taxi_trips
""").show()
```


## Question 5: User Interface

Spark's User Interface which shows the application's dashboard runs on which local port?

- 80
- 443
- 4040
- 8080
> Ans: 4040

## Question 6: Least frequent pickup location zone

Load the zone lookup data into a temp view in Spark:

```bash
wget https://d37ci6vzurychx.cloudfront.net/misc/taxi_zone_lookup.csv
```

Using the zone lookup data and the Yellow November 2025 data, what is the name of the LEAST frequent pickup location Zone?

- Governor's Island/Ellis Island/Liberty Island
- Arden Heights
- Rikers Island
- Jamaica Bay

If multiple answers are correct, select any

> Ans: Governor's Island/Ellis Island/Liberty Island <br>
>       or Arden Heights
```
df_zone = spark.read\
    .option("header", "true")\
    .csv('taxi_zone_lookup.csv')

df_zone.createOrReplaceTempView("zone_lookup")

spark.sql("""
SELECT
    z.zone,
    COUNT(*) AS trip_count
FROM taxi_trips t JOIN zone_lookup z ON t.PULocationID = z.locationid

GROUP BY 1
ORDER BY 2 
LIMIT 1
  """).show(5)
```
