# Hospital Appointment Data & RAG Assistant

## Project Overview

This project demonstrates a simple modern data engineering pipeline for a healthcare use case.

The project processes hospital appointment data, applies data quality rules, separates valid and invalid records through a quality gate, stores trusted data in Delta Lake, and produces analytics and a simple retrieval-based assistant for hospital appointment policies.

## Problem Description

Hospital appointment data can contain missing patient IDs, invalid appointment statuses, negative waiting times, and duplicate appointment IDs.

If these records are used directly, the resulting analytics may be unreliable.

The project solves this problem by validating the data before it reaches the trusted layer.

## Data Source

The project uses a small **synthetic appointment dataset** created inside the notebook.

The dataset contains:

- appointment_id
- patient_id
- department
- appointment_date
- status
- wait_time

A few intentional quality problems are added to demonstrate the FAIL and Quarantine paths.

## Workflow / Architecture

```text
Synthetic CSV Dataset
        |
        v
    Ingestion
        |
        v
      Spark
        |
        v
  Data Quality Checks
        |
        v
   Quality Gate
      /     \
   FAIL     PASS
    |         |
    v         v
Quarantine  Trusted Data
 Delta       |
             v
        Delta Lake
             |
       +-----+------+
       |            |
       v            v
   Analytics      RAG
                    |
                    v
              Hospital Policies
                    |
                    v
                  Answer
```

## Data Quality Checks

The project uses four quality checks:

### 1. Completeness

`patient_id` must not be missing.

### 2. Validity

The appointment status must be one of:

- Completed
- Cancelled
- No Show

### 3. Accuracy / Business Rule

`wait_time` cannot be negative.

### 4. Uniqueness

`appointment_id` must appear only once in the batch.

## Quality Gate

The quality gate creates two paths:

### PASS

Records that pass all required checks continue to the trusted layer and are stored in Delta Lake.

### FAIL

Records that fail one or more checks are sent to the Quarantine Delta table.

The notebook demonstrates both PASS and FAIL cases.

## Lakehouse / Delta Lake

The project stores:

- **Trusted data** in the `appointments_trusted` Delta table.
- **Failed records** in the `quarantine` Delta table.

A transformation is also applied to trusted data by creating a `wait_category` column:

- Short
- Medium
- Long

## AI / RAG Output

The project includes a small retrieval-based assistant for hospital appointment policies.

The knowledge base contains policies such as:

- arrival time
- cancellation rules
- changing appointments
- Cardiology availability
- Dermatology availability

A BM25 retriever finds the policy most related to the user's question.

The retrieved policy is then used as the answer source.

A simple confidence check is also demonstrated for questions that are not covered by the knowledge base.

## Analytics Output

The trusted appointment data is used to calculate:

- appointments by department
- average waiting time by department
- appointment status counts

## Results

The notebook demonstrates an end-to-end pipeline where:

1. Raw appointment data enters the system.
2. Spark ingests and processes the data.
3. Data quality checks identify invalid records.
4. The quality gate separates PASS and FAIL records.
5. Failed records are stored in Quarantine.
6. Passed records are stored as trusted Delta data.
7. Trusted data is used for analytics.
8. Hospital policy information is retrieved to answer appointment questions.

## Technologies Used

- Python
- Apache Spark / PySpark
- Delta Lake
- BM25
- Jupyter Notebook / Google Colab
- CSV
- GitHub

## How to Run the Project

1. Open the notebook in Google Colab or a compatible Jupyter environment.
2. Run the setup cells.
3. Run the cells from top to bottom.
4. Review the data quality results.
5. Review the PASS and FAIL paths.
6. Review the Delta Lake tables.
7. Run the analytics cells.
8. Try the RAG questions and the pair experiments.

## Future Improvements

Possible future improvements include:

- Use a real public hospital appointment dataset.
- Add streaming ingestion using Kafka.
- Use embeddings and a vector database for semantic retrieval.
- Connect the RAG component to an LLM.
- Add monitoring and data quality dashboards.
- Add more healthcare business rules.
- Deploy the pipeline as a production service.

## SDAIA Academy

SDAIA Academy GitHub Repository:

https://github.com/SDAIAAcademy
