# 🚀 Automated ETL Pipeline on AWS (S3, Lambda, Glue, EventBridge, SNS)

This project demonstrates how to build an **automated, serverless ETL pipeline** using core AWS services — perfect for **beginners in cloud, DevOps, or data engineering**.

---

## 🧠 Project Overview

The pipeline performs these steps:

1. **Upload data** to Amazon S3  
2. **Trigger AWS Lambda** on file upload  
3. **Run ETL job** using AWS Glue  
4. **Use EventBridge** to route and track events  
5. **Send notifications** through Amazon SNS  
6. **Monitor the process** with CloudWatch

---

## 🏗️ Architecture Diagram
![Architecture Diagram](https://github.com/user-attachments/assets/55796cbb-4343-4048-a1f3-a74c2efbda3e)

**Services Used:**
- 🗂️ Amazon S3 — storage for raw and processed data  
- ⚙️ AWS Lambda — event-driven compute trigger  
- 🧹 AWS Glue — ETL transformation  
- 🕓 EventBridge — workflow orchestration  
- 📧 SNS — real-time alerts  
- 📊 CloudWatch — monitoring and logs  

---

## ⚙️ Setup Steps

### 1️⃣ Create S3 Buckets
- `raw-data-bucket`
- `processed-data-bucket`

Upload your dataset (e.g., `sample_data.csv`) to `raw-data-bucket`.

---

### 2️⃣ Create a Lambda Function
- Runtime: Python 3.9+
- Add the code from [`lambda_function/lambda_function.py`](lambda_function/lambda_function.py)
- Grant permissions:
  - S3 read access
  - Glue job trigger permission
  - CloudWatch logs

Set S3 **event notification** to trigger Lambda on file upload.

---

### 3️⃣ Create AWS Glue Job
- Upload the script [`glue_job/glue_etl_script.py`](glue_job/glue_etl_script.py)
- Configure:
  - Input: `raw-data-bucket`
  - Output: `processed-data-bucket`
  - IAM role: full Glue + S3 access

---

### 4️⃣ Configure EventBridge
- Create a rule to trigger SNS after Glue job completion.
- Use [`eventbridge/eventbridge_rule.json`](eventbridge/eventbridge_rule.json) for setup.

---

### 5️⃣ Setup SNS
- Create a topic named `etl-notification-topic`
- Subscribe your email for notifications.

---

### 6️⃣ Monitor with CloudWatch
- View logs for Lambda and Glue.
- Add custom metrics or alarms as needed.

---

## 🧩 Lambda Function Example

```python
import boto3
import json

def lambda_handler(event, context):
    glue = boto3.client('glue')
    
    response = glue.start_job_run(
        JobName='etl-glue-job'
    )
    
    print("Triggered Glue job:", response['JobRunId'])
    return {
        'statusCode': 200,
        'body': json.dumps('Glue ETL Job Started!')
    }
```
## Glue Script Example

```python
import sys
from awsglue.utils import getResolvedOptions
from awsglue.context import GlueContext
from pyspark.context import SparkContext

args = getResolvedOptions(sys.argv, ['JOB_NAME'])
sc = SparkContext()
glueContext = GlueContext(sc)
spark = glueContext.spark_session

# Read from S3
df = spark.read.csv("s3://raw-data-bucket/sample_data.csv", header=True, inferSchema=True)

# Basic transformation
df_cleaned = df.dropna()

# Write processed data
df_cleaned.write.mode("overwrite").csv("s3://processed-data-bucket/cleaned_data/")

```

## 🧩 EventBridge Rule Example (eventbridge_rule.json)

```python
{
  "Source": ["aws.glue"],
  "DetailType": ["Glue Job State Change"],
  "Detail": {
    "jobName": ["etl-glue-job"],
    "state": ["SUCCEEDED"]
  }
}
```

## 🔔 Notifications (SNS)

When the Glue job succeeds, EventBridge triggers SNS to send an email notification like:

✅ ETL Job Completed Successfully!

## 🧰 Prerequisites

* AWS Account

* Basic AWS Console Knowledge

* Optional: Python & PySpark knowledge


🎥 Original Tutorial Video

📚 Beginner-friendly AWS ETL project for students and data enthusiasts.

## 🏷️ Keywords

aws etl pipeline, aws glue, lambda s3 trigger, eventbridge sns, serverless, data engineering, aws cloud project, etl automation
