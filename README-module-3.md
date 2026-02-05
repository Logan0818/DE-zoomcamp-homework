Q1 - What is count of records for the 2024 Yellow Taxi Data? <br>
```
select count(VendorID) from `zoomcamp.yellow_tripdata_non_partitioned`
```

Q2 - Write a query to count the distinct number of PULocationIDs for the entire dataset on both the tables. <br>
What is the estimated amount of data that will be read when this query is executed on the External Table and the Table? <br>
```
select count(*) from `zoomcamp.external_yellow_tripdata`
select count(*) from `zoomcamp.yellow_tripdata_non_partitioned`
```
Q3 - Write a query to retrieve the PULocationID from the table (not the external table) in BigQuery. Now write a query to retrieve the PULocationID and DOLocationID on the same table. <br>
Why are the estimated number of Bytes different? <br>
BigQuery is a columnar database, and it only scans the specific columns requested in the query. Querying two columns (PULocationID, DOLocationID) requires reading more data than querying one column (PULocationID), leading to a higher estimated number of bytes processed.

Q4 - How many records have a fare_amount of 0? <br>
```
select count(*) from `zoomcamp.yellow_tripdata_non_partitioned`
where fare_amount = 0;
```
Q5 - What is the best strategy to make an optimized table in Big Query if your query will always filter based on tpep_dropoff_datetime and order the results by VendorID (Create a new table with this strategy) <br>

```
-- Creating a partition and cluster table
CREATE OR REPLACE TABLE zoomcamp.yellow_tripdata_partitioned_clustered
PARTITION BY DATE(tpep_dropoff_datetime)
CLUSTER BY VendorID AS
SELECT * FROM zoomcamp.external_yellow_tripdata;
```

--Q6 - Write a query to retrieve the distinct VendorIDs between tpep_dropoff_datetime 2024-03-01 and 2024-03-15 (inclusive) <br>
Use the materialized table you created earlier in your from clause and note the estimated bytes. Now change the table in the from clause to the partitioned table <br>
you created for question 5 and note the estimated bytes processed. What are these values? <br>
```
--310.24MB
SELECT distinct VendorId 
FROM zoomcamp.yellow_tripdata_non_partitioned
WHERE DATE(tpep_dropoff_datetime) BETWEEN '2024-03-01' AND '2024-03-15';
```
```
--26.84MB
SELECT distinct VendorId 
FROM zoomcamp.yellow_tripdata_partitioned_clustered
WHERE DATE(tpep_dropoff_datetime) BETWEEN '2024-03-01' AND '2024-03-15';
```

Q9 - Write a SELECT count(*) query FROM the materialized table you created. How many bytes does it estimate will be read? Why? <br>
```
select count(*)
from zoomcamp.yellow_tripdata_non_partitioned;
```
```
select count(*)
from zoomcamp.yellow_tripdata_partitioned_clustered;
```
select count(*) from zoomcamp.external_yellow_tripdata;
