# ADS 509 Final Project

## Job Skill Extraction from Data Scientist Postings

### Project Overview

This project collects job postings from LinkedIn and Indeed and applies text mining and LLM-based methods to extract required skills, keywords, and trends. Results are used to compare market demand against candidate resumes.

### Data Collection

Job postings were collected using the Python JobSpy library for the role “data scientist” in the San Francisco Bay Area. JobSpy provides a unified interface and can be extended to additional sources without modifying downstream analysis.

### Data Variability and Reproducibility

Job postings are scraped live at runtime and may change between executions due to new postings, expired listings, or platform ranking differences. To support reproducibility, a snapshot of the scraped data (100 postings per source) is included in this repository. All downstream analysis is performed on the saved CSV files unless the scraper is explicitly re run.

### Repository Structure

- notebooks/: analysis notebooks in execution order
- data/raw/: raw scraped job postings
- data/processed/: cleaned and merged datasets
- src/: source code for data processing and analysis
- reports/: generated analysis reports and visualizations
- README.md: project overview and instructions
