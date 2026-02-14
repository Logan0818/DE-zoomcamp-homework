[Homework link] (https://github.com/DataTalksClub/data-engineering-zoomcamp/blob/main/cohorts/2026/04-analytics-engineering/homework.md) <br>
submission link https://courses.datatalks.club/de-zoomcamp-2026/homework/hw4

**Question 1. dbt Lineage and Execution** <br?
If you run dbt run --select int_trips_unioned, what models will be built? <br>
Ans: int_trips_unioned only <br>
because you only select to run one model, dbt will only run model. IF you want to run all upstream dependencies, you need to add + sign before the model name. <br>

**Question 2. dbt Tests** <br>
You've configured a generic test like this in your schema.yml:
```
columns:
  - name: payment_type
    data_tests:
      - accepted_values:
          arguments:
            values: [1, 2, 3, 4, 5]
            quote: false
```
Your model fct_trips has been running successfully for months. A new value 6 now appears in the source data.

What happens when you run dbt test --select fct_trips?
Ans: dbt fails the test with non-zero exit code <br>

**Question 3. Counting Records in fct_monthly_zone_revenue**<br>
After running your dbt project, query the fct_monthly_zone_revenue model. <br>
What is the count of records in the fct_monthly_zone_revenue model? <br>
```
select count(*) as total_trips
from {{ref("fct_monthly_zone_revenue")}}
```

**Question 4. Best Performing Zone for Green Taxis (2020)** <br>
Using the fct_monthly_zone_revenue table, find the pickup zone with the highest total revenue (revenue_monthly_total_amount) for Green taxi trips in 2020.<br>
Which zone had the highest revenue?<br>
```
with result as (
    select  *
    from {{ref('fct_trips')}} f
    where service_type = 'green'
        and year(pickup_datetime) = 2020
)
select pickup_borough, 
    pickup_zone,
    sum(total_amount) as total_revenue
from result
group by pickup_borough, pickup_zone
order by total_revenue desc
```
**Question 5. Green Taxi Trip Counts (October 2019)** <br>
Using the fct_monthly_zone_revenue table, what is the total number of trips (total_monthly_trips) for Green taxis in October 2019? <br>
```
with result as (
    select   *
    from {{ref('fct_trips')}} f
    where service_type = 'green'
        and year(pickup_datetime) = 2019
        and month(pickup_datetime) = 10
)
select count(*) as total_trips
from result
```
**Question 6. Build a Staging Model for FHV Data** <br>
Create a staging model for the For-Hire Vehicle (FHV) trip data for 2019.<br>
Load the FHV trip data for 2019 into your data warehouse<br>
Create a staging model stg_fhv_tripdata with these requirements:<br>
* Filter out records where dispatching_base_num IS NULL 
* Rename fields to match your project's naming conventions (e.g., PUlocationID → pickup_location_id) <br>
What is the count of records in stg_fhv_tripdata?<br>
```
select count(*)
from {{ source('raw_data_fhv', 'fhv_tripdata') }}
where dispatching_base_num is not null
```
