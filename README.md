# Data Engineering Project Using PySpark

## Overview
This repository showcases a PySpark-based data engineering workflow. It demonstrates reading, transforming, and summarizing attendance data from various sources.

## Setup
1. Clone the repository.
2. Install Python 3.11+ and dependencies (e.g., pip install pyspark).
3. Ensure your environment (e.g., Databricks, local Spark, etc.) is properly configured.

## Usage
1. Open the DataEngineerProject_SchoolData.ipynb notebook in your environment.
2. Update file paths as needed in the “Load CSV files…” cell.
3. Run all cells to produce the weekly attendance summaries.

## Findings
- Joined multiple datasets (Attendance, Organisations, Students, etc.) based on key fields.
- Discovered that “National Curriculum Year” and “Year Group” can differ; we decided to rely on “National Curriculum Year” for consistency.
- Observed potential discrepancies in attendance marks (e.g., missing or ambiguous codes) requiring refinement.

## Decisions Made
- Used PySpark for handling large attendance data (16 million+ rows).
- Unified date references by adding a “datekey” column to facilitate joins with the date dimension table.
- Chose to store final attendance summary results in parquet format for better performance and compression.

## Note to the Data Analyst
- Pay special attention to “mark” values and their mapping; some may need manual recoding.
- Consider the difference between authorized/unauthorized absences and late marks when finalizing calculations.
- Review “National Curriculum Year” values for partial or withdrawn students to ensure accurate weekly attendance calculations.

## Credits
This activity is aimed at engineering attendance data with PySpark for further analytical reporting.

