# LAB: Google Cloud Fundamentals: Getting Started with BigQuery

## OBJECTIVES:
In this lab, you learn how to perform the following tasks:
    - Load data from Cloud Storage into BigQuery.

    - Perform a query on the data in BigQuery.

## STEPS:

1. Load data from Cloud Storage into BigQuery in the Console.
   
   - Create a new dataset within your project by selecting your project in the Resources section, then clicking on CREATE DATASET on the right.

        bq --location=US mk --dataset qwiklabs-gcp-02-176f21f0e688:logdata

    - Create a new table in the logdata to store the data from the CSV file.
  
        bq mk --external_table_definition=gs://cloudtraining/gcpfci/access_log.csv logdata.accesslog


2. Perform a query on the data using the BigQuery web UI.
   
   - In the Query editor window, type (or copy-and-paste) the following query:
  
        bq query "select int64_field_6 as hour, count(*) as hitcount from logdata.accesslog group by hour order by hour"


    - At the Cloud Shell prompt, enter this command:

        bq query "select string_field_10 as request, count(*) as requestcount from logdata.accesslog group by request order by requestcount desc"

            # RESULT: This should return the expected result from the table queried.