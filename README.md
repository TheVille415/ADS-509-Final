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

## 3. Installation and Execution

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

## 4. Detailed Pipeline Step (Notebook 01)

I've converted the run configuration into a Markdown table for better visual organization.

```markdown
## 🛠 01. Data Collection Pipeline
**Notebook:** `01_data_collection_jobspy.ipynb`

### Run Configuration
| Parameter | Value |
| :--- | :--- |
| **Search Term** | "data scientist" |
| **Results Per Site** | 100 |
| **Location** | "San Francisco Bay Area" |
| **Sites** | ["linkedin", "indeed"] |

### Key Processing Steps
* **Standardization:** HTML artifacts are removed and whitespace is normalized.
* **Unified Text Field:** A new `text` field is created by joining `title + description`.
* **DuckDB Merge:** Uses SQL-based merging to ensure deterministic runs.
* **Deduplication:** Uses `job_url` or a fallback signature: `title | company | location | md5(first 500 characters of description)`.

**Output Path:** `data/processed/jobs_combined.csv`

---

## 5. Analysis and GenAI Sections

```markdown
## 📊 Analysis & GenAI Integration

### 02. Text EDA Baseline
`02_text_eda_baseline.ipynb`
* Performs descriptive statistics and word frequency analysis.
* Identifies common keywords and prepares the corpus.

### 03. Resume Skill Analysis
`03_resume_skill_analysis.ipynb`
* Compares candidate resumes against market data.
* Performs skill matching, gap identification, and alignment scoring.

### 04. GenAI Integration
`genai_integration.ipynb`
* Uses LLMs for structured skill extraction and categorization (e.g., Cloud, ML, Programming).
* Provides semantic comparison between resumes and market demand.

---

## 📝 Reproducibility Notes
* Job postings are scraped live; timestamped snapshots are saved in `data/raw/` to preserve specific runs.
* Always use `data/processed/jobs_combined.csv` for downstream tasks unless re-scraping.
