# ADS 509 Final Project - JobToken.io

## Job Skill Extraction from Data Scientist Postings

---

## 📖 Project Overview

This project builds a reproducible data pipeline to collect, standardize, and analyze job postings for the role **Data Scientist** in the San Francisco Bay Area.

**The primary goals are to:**

* **Scrape** live job postings from LinkedIn and Indeed.
* **Standardize** and clean job description text.
* **Merge and deduplicate** postings across platforms.
* **Construct** a unified NLP-ready text corpus.
* **Compare** extracted market skills against candidate resumes.

> **Final Dataset:** 200 balanced postings with 100 per source.

---

## 📂 Repository Structure

```text
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
```

## Installation and Execution

```markdown
## ⚙️ Installation & Execution

### Local Environment
```bash
pip install -r requirements.txt
pip install python-jobspy pandas numpy pyarrow duckdb
```

## Notebook Execution Order

Run the notebooks in this specific order to maintain data integrity:

1. 01_data_collection_jobspy.ipynb
2. 02_text_eda_baseline.ipynb
3. 03_resume_skill_analysis.ipynb
4. 04_genai_skill_extraction.ipynb

## Data Collection Pipeline
**Notebook:** `01_data_collection_jobspy.ipynb`

This notebook constructs a reproducible data collection pipeline to build a real-world dataset of Data Scientist job postings.

### Run Configuration
| Parameter | Value |
| :--- | :--- |
| **SEARCH_TERM** | "data scientist" |
| **RESULTS_PER_SITE** | 100 |
| **LOCATION** | "San Francisco Bay Area" |
| **SITES** | ["linkedin", "indeed"] |

*A timestamp (`RUN_TS`) is created each run to version outputs: `YYYYMMDD_HHMMSS`.*

---

### Pipeline Workflow

#### Step 1 & 2: Scrape and Snapshot
The pipeline scrapes 100 postings from both LinkedIn and Indeed.
* **LinkedIn Output:** `data/raw/jobs_linkedin_<timestamp>.csv`
* **Indeed Output:** `data/raw/jobs_indeed_<timestamp>.csv`

#### Step 3: Schema Standardization
Standardizes platform outputs to ensure consistent downstream analysis.
* **Cleaning:** Removes HTML artifacts and normalizes whitespace.
* **Feature Engineering:** Constructs a unified `text` field by combining `title` + `description`.

#### Step 4: Controlled Dataset Merge
Uses **DuckDB** to combine only the two files generated during the current run, preventing historical data accumulation.
```sql
CREATE OR REPLACE TABLE combined_jobs AS
SELECT * FROM read_csv_auto('linkedin_file')
UNION ALL
SELECT * FROM read_csv_auto('indeed_file');
```

---

## Analysis Notebooks

### 02. Text EDA Baseline
**Notebook:** `02_text_eda_baseline.ipynb`

This notebook performs exploratory text analysis on the combined dataset. Key tasks include:
* **Descriptive Statistics:** Analyzing the distribution of job postings by source and location.
* **Token Analysis:** Word frequency analysis and common keyword identification.
* **Corpus Preparation:** Cleaning and preparing the text for downstream skill extraction.

---

### 03. Resume Skill Analysis
**Notebook:** `03_resume_skill_analysis.ipynb`

This notebook compares extracted job market skills against a candidate resume. Steps include:
* **Resume Ingestion:** Parsing text from a candidate's resume.
* **Skill Matching:** Identifying overlaps between the resume and the job corpus.
* **Gap Analysis:** Highlighting missing skills based on current market demand.
* **Alignment Scoring:** Generating a numerical score for candidate-to-market fit.

---

## 04. GenAI Integration
**Notebook:** `04_genai_skill_extraction.ipynb`

This notebook integrates a Large Language Model (LLM) to extend baseline NLP methods. Typical tasks include:
* **Structured Skill Extraction:** Using LLMs to pull specific skills from dense descriptions.
* **Categorization:** Automatically grouping skills into categories like Programming, Cloud, or ML Frameworks.
* **Market Summarization:** Generating high-level summaries of market demand trends.
* **Semantic Comparison:** Performing deep semantic matching between resume content and market requirements.

### Groq API Setup (Required for Notebook 04)

Notebook `04_genai_skill_extraction.ipynb` uses the Groq API (LLaMA 3.1) to extract structured skills.

### 1) Create a Groq API key
Go to: https://console.groq.com/

Create an API key from your dashboard.

### 2) Add your key to a `.env` file
Create a file named `.env` in the project root (same level as `README.md`):

```bash
GROQ_API_KEY=your_api_key_here
```

### 3) Do not commit .env

Make sure .env is listed in your .gitignore.

### 4) Verify it loads

Notebook 04 loads the key using python-dotenv and expects GROQ_API_KEY to be available.

---

## ✅ Quick `.gitignore` line (if you need it)

Add this to `.gitignore`:

```gitignore
.env
```

---

## 📝 Reproducibility Notes
* **Live Data:** Job postings are scraped live and may change between runs.
* **Snapshots:** To preserve results, timestamped raw snapshots are saved in `data/raw/`.
* **Default Data:** Downstream analysis should always use `data/processed/jobs_combined_dedup.csv` unless a full re-scrape is intended.
