# ADS 509 Final Project - JobToken.io

## Job Skill Extraction from Data Scientist Postings

---

## Project Overview

This project builds a reproducible data pipeline to collect, standardize, and analyze job postings for the role **Data Scientist** in the San Francisco Bay Area.

The primary goals of this project are to:

- Scrape live job postings from LinkedIn and Indeed
- Standardize and clean job description text
- Merge and deduplicate postings across platforms
- Construct a unified NLP ready text corpus
- Compare extracted market skills against candidate resumes

The final dataset contains **200 balanced postings** with **100 per source**.

---

## Repository Structure

Final Project/
├── data/
│   ├── raw/
│   └── processed/
├── notebooks/
│   ├── 01_data_collection_jobspy.ipynb
│   ├── 02_text_eda_baseline.ipynb
│   ├── 03_resume_skill_analysis.ipynb
│   └── genai_integration.ipynb
├── outputs/
├── requirements.txt
└── README.md

---

## Notebooks and Execution Order

Run the notebooks in this order:

1. `01_data_collection_jobspy.ipynb`
2. `02_text_eda_baseline.ipynb`
3. `03_resume_skill_analysis.ipynb`
4. `genai_integration.ipynb`

---

## Installation

### Local

```bash
pip install -r requirements.txt

Google Colab

!pip install python-jobspy pandas numpy pyarrow duckdb
```

⸻

01 Data Collection Pipeline

Notebook: 01_data_collection_jobspy.ipynb

This notebook constructs a reproducible data collection pipeline to build a real world dataset of Data Scientist job postings.

Run Configuration

SEARCH_TERM = "data scientist"
RESULTS_PER_SITE = 100
LOCATION = "San Francisco Bay Area"
SITES = ["linkedin", "indeed"]

A timestamp is created each run to keep outputs versioned:

RUN_TS = YYYYMMDD_HHMMSS

⸻

Step 1 Scrape LinkedIn and Save

The pipeline scrapes 100 postings from LinkedIn and saves the raw snapshot as a timestamped CSV file:
 • Output: data/raw/jobs_linkedin_<timestamp>.csv

⸻

Step 2 Scrape Indeed and Save

The pipeline scrapes 100 postings from Indeed and saves the raw snapshot as a timestamped CSV file:
 • Output: data/raw/jobs_indeed_<timestamp>.csv

⸻

Step 3 Schema Standardization

Because platform outputs can vary, the notebook standardizes the schema to keep downstream analysis consistent.

Standardized Columns

source
search_term
title
company
location
date_posted
job_type
description
job_url
text

Cleaning Performed
 • HTML artifacts removed from descriptions
 • Whitespace normalized
 • Missing columns created when absent
 • A unified NLP ready text field constructed:

text = title + description

⸻

Step 4 Controlled Dataset Merge Using DuckDB

Only the two files generated during the current run are combined, preventing historical accumulation.

Example merge logic:

CREATE OR REPLACE TABLE combined_jobs AS
SELECT *FROM read_csv_auto('linkedin_file')
UNION ALL
SELECT* FROM read_csv_auto('indeed_file');

This produces a deterministic combined dataset for the current run.

⸻

Step 5 Deduplication Strategy

A two stage deduplication process improves data quality.

Primary Deduplication
 • Deduplicate using normalized job_url when available

Fallback Deduplication
When job_url is missing, a stable signature is generated:

title | company | location | md5(first 500 characters of description)

Duplicates are removed using this signature.

⸻

Step 6 Balanced Dataset Enforcement

To ensure platform balance, the notebook caps the dataset to 100 postings per source.
 • If date_posted exists, the most recent postings are retained
 • Final result:

200 total postings
100 LinkedIn
100 Indeed

⸻

Final Output

The processed dataset is saved to:

data/processed/jobs_combined.csv

All downstream notebooks should use this file unless the scraper is intentionally re run.

⸻

02 Text EDA Baseline

Notebook: 02_text_eda_baseline.ipynb

This notebook performs exploratory text analysis on the combined dataset. Typical tasks include:
 • Descriptive statistics
 • Token and word frequency analysis
 • Common keyword identification
 • Corpus preparation for skill extraction

⸻

03 Resume Skill Analysis

Notebook: 03_resume_skill_analysis.ipynb

This notebook compares extracted job market skills against a candidate resume. Steps include:
 • Resume text ingestion
 • Skill matching against the job corpus
 • Frequency comparison
 • Gap identification
 • Alignment scoring

⸻

GenAI Integration

Notebook: genai_integration.ipynb

This notebook integrates a Large Language Model to extend baseline NLP methods. Typical tasks include:
 • Structured skill extraction from job descriptions
 • Skill categorization, for example programming, cloud, ML frameworks
 • Summary of market demand trends
 • Semantic comparison between resume and market skills

⸻

Reproducibility Notes

Job postings are scraped live and may change between runs due to:
 • New postings
 • Expired listings
 • Platform ranking changes

To preserve reproducibility:
 • Timestamped raw snapshots are saved in data/raw/
 • Downstream analysis should use data/processed/jobs_combined.csv unless re scraping is intended

⸻

Summary

This project implements an applied data science workflow that combines:
 • Real world job market data collection
 • Data cleaning and standardization
 • DuckDB based merging for deterministic runs
 • Deduplication and dataset balancing
 • NLP based skill extraction and comparison
 • LLM based enhancement for structured skill extraction and summarization
