## Week 1 Homework

In this homework we'll prepare the environment 
and practice with terraform and SQL

## Question 1. Google Cloud SDK

Install Google Cloud SDK. What's the version you have? 

To get the version, run `gcloud --version`

Answer:
```
Google Cloud SDK 370.0.0

Output:

(base) ademouy@de-zoomcamp:~/data-engineering-zoomcamp/week_1_basics_n_setup$ gcloud --version
Google Cloud SDK 370.0.0
alpha 2022.01.21
beta 2022.01.21
bq 2.0.73
core 2022.01.21
gsutil 5.6
minikube 1.24.0
skaffold 1.35.1
```
## Google Cloud account 

Create an account in Google Cloud and create a project.


## Question 2. Terraform 

Now install terraform and go to the terraform directory (`week_1_basics_n_setup/1_terraform_gcp/terraform`)

After that, run

* `terraform init`
* `terraform plan`
* `terraform apply` 

Apply the plan and copy the output (after running `apply`) to the form.

It should be the entire output - from the moment you typed `terraform init` to the very end.

Answer:
```
(base) ademouy@de-zoomcamp:~/data-engineering-zoomcamp/week_1_basics_n_setup/1_terraform_gcp/terraform$ terraform apply
var.project
  Your GCP Project ID

  Enter a value: ny-rides-alex


Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # google_bigquery_dataset.dataset will be created
  + resource "google_bigquery_dataset" "dataset" {
      + creation_time              = (known after apply)
      + dataset_id                 = "trips_data_all"
      + delete_contents_on_destroy = false
      + etag                       = (known after apply)
      + id                         = (known after apply)
      + last_modified_time         = (known after apply)
      + location                   = "europe-west6"
      + project                    = "ny-rides-alex"
      + self_link                  = (known after apply)

      + access {
          + domain         = (known after apply)
          + group_by_email = (known after apply)
          + role           = (known after apply)
          + special_group  = (known after apply)
          + user_by_email  = (known after apply)

          + view {
              + dataset_id = (known after apply)
              + project_id = (known after apply)
              + table_id   = (known after apply)
            }
        }
    }

  # google_storage_bucket.data-lake-bucket will be created
  + resource "google_storage_bucket" "data-lake-bucket" {
      + force_destroy               = true
      + id                          = (known after apply)
      + location                    = "EUROPE-WEST6"
      + name                        = "dtc_data_lake_ny-rides-alex"
      + project                     = (known after apply)
      + self_link                   = (known after apply)
      + storage_class               = "STANDARD"
      + uniform_bucket_level_access = true
      + url                         = (known after apply)

      + lifecycle_rule {
          + action {
              + type = "Delete"
            }

          + condition {
              + age                   = 30
              + matches_storage_class = []
              + with_state            = (known after apply)
            }
        }

      + versioning {
          + enabled = true
        }
    }

Plan: 2 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

google_bigquery_dataset.dataset: Creating...
google_storage_bucket.data-lake-bucket: Creating...
google_bigquery_dataset.dataset: Creation complete after 1s [id=projects/ny-rides-alex/datasets/trips_data_all]
google_storage_bucket.data-lake-bucket: Creation complete after 2s [id=dtc_data_lake_ny-rides-alex]

Apply complete! Resources: 2 added, 0 changed, 0 destroyed.
```

## Prepare Postgres 

Run Postgres and load data as shown in the videos

We'll use the yellow taxi trips from January 2021:

```bash
wget https://s3.amazonaws.com/nyc-tlc/trip+data/yellow_tripdata_2021-01.csv
```

You will also need the dataset with zones:

```bash 
wget https://s3.amazonaws.com/nyc-tlc/misc/taxi+_zone_lookup.csv
```

Download this data and put it to Postgres

## Question 3. Count records 

How many taxi trips were there on January 15?

Consider only trips that started on January 15.


Answer: 
```
SELECT COUNT(1) 
	FROM yellow_taxi_data
	WHERE tpep_pickup_datetime::date = '2021-01-15';

Output:

| count |
|-------|
| 53024 |
```

## Question 4. Largest tip for each day

Find the largest tip for each day. 
On which day it was the largest tip in January?

Use the pick up time for your calculations.

(note: it's not a typo, it's "tip", not "trip")

Answer: 
```
SELECT tpep_pickup_datetime, MAX(tip_amount) 
    FROM yellow_taxi_data 
    GROUP BY 1
    ORDER BY 2 DESC LIMIT 1;

Output:

|----------------------|---------|
| tpep_pickup_datetime | max     |
|----------------------|---------|
| 2021-01-20 11:22:05  | 1140.44 |
```

## Question 5. Most popular destination

What was the most popular destination for passengers picked up 
in central park on January 14?

Use the pick up time for your calculations.

Enter the zone name (not id). If the zone name is unknown (missing), write "Unknown" 

Answer: 
```
SELECT t."DOLocationID", z."Zone", COUNT(z."Zone")
	FROM yellow_taxi_data t
	LEFT JOIN zones z
	    ON z."LocationID" = t."DOLocationID"
	GROUP BY 1, 2
	ORDER BY 3 DESC 
	LIMIT 1;

Output:

|--------------|-----------------------|-------|
| DOLocationID | Zone                  | count |
|--------------|-----------------------|-------|
| 236          | Upper East Side North | 73700 |
```

## Question 6. Most expensive locations

What's the pickup-dropoff pair with the largest 
average price for a ride (calculated based on `total_amount`)?

Enter two zone names separated by a slash

For example:

"Jamaica Bay / Clinton East"

If any of the zone names are unknown (missing), write "Unknown". For example, "Unknown / Clinton East". 

Answer:
```
SELECT "PULocationID", "DOLocationID", AVG(total_amount)
  FROM yellow_taxi_data
  GROUP BY 1, 2
  ORDER BY 3 DESC
  LIMIT 1

SELECT "LocationID", "Zone" 
  FROM zones 
  WHERE "LocationID" = 4 OR "LocationID" = 256


  Output:

+--------------+--------------+----------+
| PULocationID | DOLocationID | avg      |
|--------------+--------------+----------|
| 4            | 265          | 2292.4

+------------+---------------+
| LocationID | Zone          |
|------------+---------------|
| 4          | Alphabet City |
| 265        | <null>        |
+------------+---------------+
```
## Submitting the solutions

* Form for submitting: https://forms.gle/yGQrkgRdVbiFs8Vd7
* You can submit your homework multiple times. In this case, only the last submission will be used. 

Deadline: 26 January (Wednesday), 22:00 CET

