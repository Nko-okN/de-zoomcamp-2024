# Docker and SQL

## 1
RTA: --rm will automatically remove the container when exits
```bash
docker run -it --rm ubuntu bash
```

## 2
RTA: wheel 0.42.0

```bash
docker run -it --rm python:3.9

root@06858efcce30:/# pip list
Package    Version
---------- -------
pip        23.0.1
setuptools 58.1.0
wheel      0.42.0
```

## 3
RTA: 15412

```sql
SELECT COUNT(1) 
FROM green_taxi_data 
WHERE lpep_pickup_datetime::date = '2019-09-18'
    AND lpep_dropoff_datetime::date = '2019-09-18';
```

## 4 
RTA: 2019-09-26

```sql
SELECT lpep_pickup_datetime::date 
FROM green_taxi_data 
WHERE trip_distance = (SELECT MAX(trip_distance) FROM green_taxi_data);
```

## 5
RTA: Brooklyn, Manhattan, Queens

```sql
SELECT 
    Borough,
    SUM(fare_amount) AS total_amount
FROM green_taxi_data
JOIN taxi_zone ON PULocationID = LocationID
WHERE lpep_pickup_datetime::date = '2019-09-18'
    AND Borough !="Unknown"
GROUP BY Borough
HAVING SUM(fare_amount) > 50000;
```

## 6
RTA: JFK Airport

```sql
WITH pickup_cte AS (
    SELECT DISTINCT
        tz."PULocationID",
        gtd.lpep_pickup_datetime::date AS pickup_date,
        gtd.lpep_dropoff_datetime::date AS dropoff_date,
        tz."Zone",
        tz."DOLocationID",
        gtd.tip_amount
    FROM taxi_zone tz
    JOIN green_taxi_data gtd ON tz."PULocationID" = tz."LocationID"
    WHERE LOWER(tz."Zone") = 'astoria'
        AND EXTRACT(MONTH FROM gtd.lpep_pickup_datetime) = 9
        AND EXTRACT(YEAR FROM gtd.lpep_pickup_datetime) = 2019
)
SELECT tz."Zone"
FROM taxi_zone tz
WHERE tz."LocationID" = (
        SELECT "DOLocationID"
        FROM pickup_cte
        WHERE tip_amount = (
                SELECT MAX(tip_amount)
                FROM pickup_cte
            )
    );
```


# Terraform

## 7
```powershell
(base) nico@dezoom:~/data-engineering-zoomcamp/01-docker-terraform/1_terraform_gcp/terraform/terraform_with_variables$ terraform apply 
# Terraform Output

## Resources to be Added
   + google_bigquery_dataset.demo_dataset
       id                           = (known after apply)
       creation_time                = (known after apply)
       dataset_id                   = "terraform_test_bq1"
       default_collation            = (known after apply)
       default_partition_expiration_ms = null
       default_table_expiration_ms  = null
       effective_labels             = (known after apply)
       etag                         = (known after apply)
       is_case_insensitive          = (known after apply)
       labels                       = null
       last_modified_time           = (known after apply)
       max_time_travel_hours        = (known after apply)
       self_link                    = (known after apply)
       storage_billing_model        = (known after apply)
       terraform_labels             = (known after apply)

       # Access Configuration
       access {
           role          = null
           user_by_email = null
       }
       access {
           role          = null
           special_group = null
       }
       access {
           role          = null
           special_group = null
       }
       access {
           role          = null
           special_group = null
       }

   + google_storage_bucket.demo-bucket
       id                           = (known after apply)
       effective_labels             = (known after apply)
       force_destroy                = true
       location                     = "US"
       name                         = "terraform_test_bucket1"
       project                      = (known after apply)
       public_access_prevention     = (known after apply)
       self_link                    = (known after apply)
       storage_class                = "STANDARD"
       terraform_labels             = (known after apply)
       uniform_bucket_level_access  = (known after apply)
       url                          = (known after apply)

       # Lifecycle Rule Configuration
       lifecycle_rule {
           action {
               type = "AbortIncompleteMultipartUpload"
           }
           condition {
               age                    = 1
               matches_prefix         = []
               matches_storage_class  = []
               matches_suffix         = []
               with_state             = (known after apply)
           }
       }

## Resources to be Destroyed
   - google_bigquery_dataset.demo_dataset
       id                           = "projects/helical-history-412202/datasets/demo_dataset"
       creation_time                = 1706462244336
       dataset_id                   = "demo_dataset"
       default_collation            = (known after apply)
       default_partition_expiration_ms = 0
       default_table_expiration_ms  = 0
       effective_labels             = (known after apply)
       etag                         = "CjU8eZtlbupsr52UTvOuIA=="
       is_case_insensitive          = false
       labels                       = null
       last_modified_time           = 1706462244336
       max_time_travel_hours        = (known after apply)
       self_link                    = "https://bigquery.googleapis.com/bigquery/v2/projects/helical-history-412202/datasets/demo_dataset"
       storage_billing_model        = (known after apply)
       terraform_labels             = (known after apply)

# Terraform Apply Output
# ----------------------

# Applying changes...

google_bigquery_dataset.demo_dataset: Destroying... [id=projects/helical-history-412202/datasets/demo_dataset]
google_storage_bucket.demo-bucket: Creating...
google_bigquery_dataset.demo_dataset: Destruction complete after 0s
google_bigquery_dataset.demo_dataset: Creating...
google_storage_bucket.demo-bucket: Creation complete after 1s [id=terraform_test_bucket1]
google_bigquery_dataset.demo_dataset: Creation complete after 1s [id=projects/helical-history-412202/datasets/terraform_test_bq1]

# Apply complete! Resources: 2 added, 0 changed, 1 destroyed.
```

