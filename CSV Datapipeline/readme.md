# CSV to S3 + Athena Pipeline

This project demonstrates how to upload CSV data to *Amazon S3* and query it using *Amazon Athena*, without relying on AWS Glue.  

## 🚀 Overview
- Upload CSV files into an S3 bucket  
- Create an Athena database and table with a defined schema  
- Run SQL queries directly on your CSV data  

## 🛠 Prerequisites
- An AWS account  
- S3 bucket created (e.g., my-csv-bucket)  
- [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html) installed and configured  
- [Athena console](https://console.aws.amazon.com/athena/) access  

## 📥 Upload CSV to S3
```bash
aws s3 cp data/myfile.csv s3://my-csv-bucket/
‘’’
### Create Athena table
CREATE EXTERNAL TABLE my_csv_db.my_table (
  id INT,
  name STRING,
  value DOUBLE
)
ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.OpenCSVSerde'
WITH SERDEPROPERTIES (
  "separatorChar" = ",",
  "quoteChar"     = "\""
)
LOCATION 's3://my-csv-bucket/'
TBLPROPERTIES ('has_encrypted_data'='false');
###Run queries
Put what we used
