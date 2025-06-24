
# AWS Sentiment Analysis Pipeline using Glue & SageMaker

This project demonstrates a serverless, scalable ETL and NLP pipeline using **AWS Glue**, **SageMaker**, **Athena**, and **S3**, powered by a Hugging Face transformer model for sentiment classification.

---

## 📊 Project Architecture

     +------------------------+
     | Amazon Reviews Dataset |
     +------------------------+
                 |
                 v
     +-------------------------+
     |   AWS Glue Crawler      |  <-- Crawls raw data
     +-------------------------+
                 |
                 v
     +-------------------------+
     |  AWS Glue Data Catalog  |  <-- Registers raw schema
     +-------------------------+
                 |
                 v
     +-------------------------+
     |   AWS Glue ETL Job      |
     | - Preprocess Reviews    |
     | - Merge heading+review  |
     | - Call HF Model (SM)    |
     | - Save to S3 (Parquet)  |
     +-------------------------+
                 |
                 v
        +---------------------+
        | S3 (Processed Data) |
        +---------------------+
                 |
                 v
     +-------------------------+
     |   AWS Glue Crawler      |  <-- Crawls transformed output
     +-------------------------+
                 |
                 v
     +-------------------------+
     |  AWS Glue Data Catalog  |  <-- Registers processed schema
     +-------------------------+
                 |
                 v
          +----------------+
          | Amazon Athena  |
          |   (SQL Query)  |
          +----------------+


---

## 🚀 What This Project Does

| Stage      | Description |
|------------|-------------|
| **Ingest** | Amazon review dataset loaded into S3 |
| **Crawl**  | Crawler registers schema in Glue Data Catalog |
| **Transform** | Glue job merges title + review, applies Hugging Face sentiment model via SageMaker endpoint |
| **Store** | Outputs sentiment-enriched data back to S3 as Parquet |
| **Analyze** | Queryable via Amazon Athena |

---

## 🤗 Model Used

- **Hugging Face Model**: [`distilbert-base-uncased-finetuned-sst-2-english`](https://huggingface.co/distilbert-base-uncased-finetuned-sst-2-english)
- **Task**: `text-classification`
- **Deployed using**: SageMaker Python SDK

---

## 🧪 Key AWS Services

- [x] **Amazon S3** – Source & target data storage  
- [x] **AWS Glue** – Catalog + ETL transformation + Data Quality  
- [x] **Amazon SageMaker** – Endpoint to host Hugging Face model  
- [x] **Amazon Athena** – Query output using standard SQL

---

## 📂 Folder Structure

```

aws-sentiment-pipeline/
├── glue-scripts/
│   └── etl\_sentiment\_analysis\_job.py
├── sagemaker/
│   └── hf\_endpoint\_deploy.ipynb
├── athena/
│   └── sample\_queries.sql
├── data-sample/
│   └── sample\_reviews.csv
└── README.md

````

---

## 🛡 IAM Role Notes

Make sure your **Glue Job's IAM Role** has permission to invoke the SageMaker endpoint:

```json
{
  "Effect": "Allow",
  "Action": "sagemaker:InvokeEndpoint",
  "Resource": "arn:aws:sagemaker:<your_region>:<your_account-id>:endpoint/hf-sentiment-endpoint"
}
````

---

## 📌 How to Run

### 1. Deploy Hugging Face Model

Run the notebook in `sagemaker/hf_endpoint_deploy.ipynb` to deploy the model.

### 2. Configure AWS Glue

* Crawler: `reviews-crawler` → populates `sentiment_pipeline_db.reviews`
* Job: `etl_sentiment_analysis_job` reads from catalog, infers sentiment, writes to S3

### 3. Query Results via Athena

Point Athena to the processed folder:

```sql
SELECT combined_text, sentiment FROM "sentiment_pipeline_db"."processed_reviews"
WHERE sentiment = 'NEGATIVE'
LIMIT 10;
```
Refer to attached sql results file for more queries.

---

## 📌 Future Enhancements

* Replace real-time inference with batch transform for cost savings
* Add unit tests and logging in the ETL script
* Create automated pipeline using Step Functions or Airflow

---

## 🧑‍💻 Author

**Hemanth Varma**
*Cloud Data & ML Engineer | AWS Certified | NLP Enthusiast*
[GitHub](https://github.com/hemanth08) • [LinkedIn](https://www.linkedin.com/in/hemanth-varma-3105/)
