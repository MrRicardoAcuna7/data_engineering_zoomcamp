Week 1 Homework
In this homework we'll prepare the environment and practice with terraform and SQL

Question 1. Google Cloud SDK
Install Google Cloud SDK. What's the version you have?

To get the version, run gcloud --version

1. --Google Cloud SDK 371.0.0

Google Cloud account
Create an account in Google Cloud and create a project.

Question 2. Terraform
Now install terraform and go to the terraform directory (week_1_basics_n_setup/1_terraform_gcp/terraform)

After that, run

terraform init
terraform plan
terraform apply
Apply the plan and copy the output (after running apply) to the form.

It should be the entire output - from the moment you typed terraform init to the very end.
2. $ terraform init

Initializing the backend...

Initializing provider plugins...
- Reusing previous version of hashicorp/google from the dependency lock file
- Using previously-installed hashicorp/google v4.9.0

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.

Richard Acuna@DESKTOP MINGW64 /c/cursos/data_engineering_zoomcamp_RA/data_engineering_zoomcamp/Week1/gcp_terraform (main)
$ terraform plan
var.project
  dtc-de-course-racuna

  Enter a value: dtc-de-course-racuna

google_storage_bucket.data-lake-bucket: Refreshing state... [id=dtc_data_lake_dtc-de-course-racuna]
google_bigquery_dataset.dataset: Refreshing state... [id=projects/dtc-de-course-racuna/datasets/trips_data_all]

Note: Objects have changed outside of Terraform

Terraform detected the following changes made outside of Terraform since the last "terraform apply":

  # google_bigquery_dataset.dataset has changed
  ~ resource "google_bigquery_dataset" "dataset" {
        id                              = "projects/dtc-de-course-racuna/datasets/trips_data_all"
      + labels                          = {}
        # (10 unchanged attributes hidden)

        # (4 unchanged blocks hidden)
    }

  # google_storage_bucket.data-lake-bucket has changed
  ~ resource "google_storage_bucket" "data-lake-bucket" {
        id                          = "dtc_data_lake_dtc-de-course-racuna"
      + labels                      = {}
        name                        = "dtc_data_lake_dtc-de-course-racuna"
        # (9 unchanged attributes hidden)


        # (2 unchanged blocks hidden)
    }


Unless you have made equivalent changes to your configuration, or ignored the relevant attributes using ignore_changes, the following plan may include actions to undo or respond to these changes.

──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── 

No changes. Your infrastructure matches the configuration.

Your configuration already matches the changes detected above. If you'd like to update the Terraform state to match, create and apply a refresh-only plan:
  terraform apply -refresh-only

Richard Acuna@DESKTOP MINGW64 /c/cursos/data_engineering_zoomcamp_RA/data_engineering_zoomcamp/Week1/gcp_terraform (main)
$ terraform apply
var.project
  dtc-de-course-racuna

  Enter a value: dtc-de-course-racuna

google_storage_bucket.data-lake-bucket: Refreshing state... [id=dtc_data_lake_dtc-de-course-racuna]
google_bigquery_dataset.dataset: Refreshing state... [id=projects/dtc-de-course-racuna/datasets/trips_data_all]

Note: Objects have changed outside of Terraform

Terraform detected the following changes made outside of Terraform since the last "terraform apply":

  # google_bigquery_dataset.dataset has changed
  ~ resource "google_bigquery_dataset" "dataset" {
        id                              = "projects/dtc-de-course-racuna/datasets/trips_data_all"
      + labels                          = {}
        # (10 unchanged attributes hidden)

        # (4 unchanged blocks hidden)
    }

  # google_storage_bucket.data-lake-bucket has changed
  ~ resource "google_storage_bucket" "data-lake-bucket" {
        id                          = "dtc_data_lake_dtc-de-course-racuna"
      + labels                      = {}
        name                        = "dtc_data_lake_dtc-de-course-racuna"
        # (9 unchanged attributes hidden)


        # (2 unchanged blocks hidden)
    }


Unless you have made equivalent changes to your configuration, or ignored the relevant attributes using ignore_changes, the following plan may include actions to undo or respond to these changes.

──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── 

No changes. Your infrastructure matches the configuration.

Your configuration already matches the changes detected above. If you'd like to update the Terraform state to match, create and apply a refresh-only plan:
  terraform apply -refresh-only

Apply complete! Resources: 0 added, 0 changed, 0 destroyed.
Prepare Postgres
Run Postgres and load data as shown in the videos

We'll use the yellow taxi trips from January 2021:

wget https://s3.amazonaws.com/nyc-tlc/trip+data/yellow_tripdata_2021-01.csv
You will also need the dataset with zones:

wget https://s3.amazonaws.com/nyc-tlc/misc/taxi+_zone_lookup.csv
Download this data and put it to Postgres

Question 3. Count records
How many taxi trips were there on January 15?

Consider only trips that started on January 15.

Question 4. Largest tip for each day
Find the largest tip for each day. On which day it was the largest tip in January?

Use the pick up time for your calculations.

(note: it's not a typo, it's "tip", not "trip")

Question 5. Most popular destination
What was the most popular destination for passengers picked up in central park on January 14?

Use the pick up time for your calculations.

Enter the zone name (not id). If the zone name is unknown (missing), write "Unknown"

Question 6. Most expensive locations
What's the pickup-dropoff pair with the largest average price for a ride (calculated based on total_amount)?

Enter two zone names separated by a slash

For example:

"Jamaica Bay / Clinton East"

If any of the zone names are unknown (missing), write "Unknown". For example, "Unknown / Clinton East".


--SELECT COUNT(1) FROM yellow_taxi_trips WHERE CAST(tpep_pickup_datetime as DATE)='20210215'
--How many taxi trips were there on January 15?
SELECT count(1) FROM yellow_taxi_trips  where cast(tpep_pickup_datetime as DATE)='2021-01-15';

--Find the largest tip for each day. On which day it was the largest tip in January?
SELECT cast(tpep_pickup_datetime as date) , max(trip_distance) FROM yellow_taxi_trips group by cast(tpep_pickup_datetime as date)
order by cast(tpep_pickup_datetime as date);

--What was the most popular destination for passengers picked up in central park on January 14?
select coalesce(tz."Zone",'Unknown'),count(yt.index) from yellow_taxi_trips yt  
left join public.taxi_zone tz on "LocationID"  = "DOLocationID"
where cast(tpep_pickup_datetime as DATE)='2021-01-14'
group by tz."Zone" order by count(yt.index) desc;

--What's the pickup-dropoff pair with the largest average price for a ride (calculated based on total_amount)?
select concat(coalesce(pu."Zone",'Unknown'),'/',coalesce(dof."Zone",'Unknown')),avg(total_amount) from yellow_taxi_trips yt  
left join public.taxi_zone dof on dof."LocationID"  = "DOLocationID"
left join public.taxi_zone pu on pu."LocationID"  = "DOLocationID"
group by concat(coalesce(pu."Zone",'Unknown'),'/',coalesce(dof."Zone",'Unknown'))
order by avg(total_amount) desc;