# Data Engineering Project Using PySpark

## Overview
This project provides a sophisticated and methodologically rigorous approach to analysing school attendance data, leveraging the distributed computing capabilities of **Apache PySpark**. The dataset consolidates multiple structured sources, encompassing institutional metadata, student demographics, and attendance records. The primary objective is to engineer a scalable, high-performance data pipeline that transforms unprocessed raw data into analytically robust structures. This pipeline underpins a comprehensive, data-driven attendance reporting system, designed to facilitate enhanced decision-making and policy development by **data analysts** and education researchers.

## Datasets Utilised
The analytical framework of this study incorporates multiple heterogeneous datasets, each serving a distinct function:

- **dim_Organisation** – Encapsulates detailed institutional metadata, covering administrative characteristics and hierarchical classifications within an educational consortium.
- **dim_Student** – Provides granular-level student-specific identifiers and biographical attributes.
- **dim_StudentExtended** – Augments core student data with enriched demographic and socioeconomic indicators.
- **fact_AttendanceSession** – Maintains session-specific attendance logs, segmented into AM and PM attendance markers for each student.
- **dim_Date** – Functions as a temporal reference dimension, furnishing a comprehensive chronological framework.

## Project Objectives
1. **Data Ingestion:** Facilitate the efficient assimilation of parquet and CSV datasets into **PySpark DataFrames**, optimising schema inference and data persistence mechanisms.
2. **Exploratory Data Analysis:** Conduct rigorous statistical profiling to delineate dataset characteristics, inter-variable relationships, and structural coherence.
3. **Summary Table Construction:** Compute **weekly attendance performance metrics** at the institutional level, stratified by Year Group.
4. **Advanced Analytical Computation:** Employ statistical and computational methodologies to extract inferential insights within the Jupyter Notebook analytical workspace.
5. **Optimised Data Storage:** Implement best practices in **Parquet-based data persistence**, ensuring optimal query efficiency and storage footprint reduction.

## Methodological Approach
Given the substantial size of the dataset (**16M+ rows**), **PySpark** was judiciously chosen for its robust distributed computing infrastructure, which allows for parallel processing and efficient memory management.

### **Methodological Workflow**
1. **Library Integration:** Import and initialise essential PySpark modules to enable seamless data manipulation.
2. **Dataset Acquisition:** Load parquet and CSV files into high-performance **DataFrames**, ensuring schema validation and type safety.
3. **Preliminary Analysis:** Conduct systematic data validation, schema inspection, and integrity checks.
4. **Data Transformation Methodologies:**
   - Establish relational linkages between disparate datasets through **efficient join operations**.
   - Analyse categorical distributions and impute inconsistencies where necessary.
   - Select and retain only analytically relevant attributes to streamline computational efficiency.
5. **Feature Engineering Strategies:**
   - Ensure consistency in attendance tracking by enforcing **two-record entries per student per day**.
   - Compute **attendance performance metrics**, utilising `is_present` and `is_possible` attributes for statistical derivation.
6. **Missing Data Handling:** Implement robust **imputation and exclusion strategies** to address sparsity issues.
7. **Descriptive and Inferential Statistics:** Generate high-level summary statistics, including mean, median, standard deviation, and outlier detection measures.
8. **Optimised Data Export:** Persist processed data in **Parquet format**, ensuring high availability and seamless integration with downstream analytical platforms.

## Implementation Details

### **PySpark Session Initialisation**
```python
from pyspark.sql import SparkSession
spark = SparkSession.builder \
    .appName("DataEngineeringProject") \
    .config("spark.driver.memory", "16g") \
    .getOrCreate()
```

### **Data Loading and Initial Processing**
```python
df_date = spark.read.option("header", True).option("inferSchema", True).csv("data/dim_Date.csv")
df_attendance = spark.read.parquet("data/fact_AttendanceSession")
df_organisation = spark.read.parquet("data/dim_Organisation")
df_student = spark.read.parquet("data/dim_Student")
df_student_ext = spark.read.parquet("data/dim_StudentExtended")
```

### **Dataset Unification Through Relational Joins**
```python
from pyspark.sql import functions as F

# Constructing a unified dataset through optimised join operations
df_combined = df_attendance.alias("att") \
    .join(df_organisation.alias("org"), "organisationkey", "left") \
    .join(df_student.alias("stu"), "studentkey", "left") \
    .join(df_student_ext.alias("stex"), "studentkey", "left") \
    .join(df_date.alias("dd"), F.regexp_replace("att.Date", "-", "").cast("int") == F.col("dd.DateKey"), "left")
```

### **Derivation of Attendance Metrics and Performance Indicators**
```python
df_summary = df_combined.groupBy("organisationkey", "Year_Group", "ISOWeekNumberOfYear") \
    .agg(F.sum("is_present").alias("total_present"),
         F.sum("is_possible").alias("total_possible")) \
    .withColumn("attendance_percentage", F.col("total_present") / F.col("total_possible") * 100)
```

### **Efficient Data Storage and Retrieval Mechanisms**
```python
df_summary.write.mode("overwrite").parquet("data/fact_AttendanceSummary.parquet")
```

## **Key Insights and Considerations**
- **Computational Scalability:** PySpark’s distributed processing architecture facilitated **real-time analytical processing** of high-volume data.
- **Data Quality and Integrity:** Implemented comprehensive validation checks to ensure **consistent tracking of student attendance records**.
- **Handling Missing Data:** Adopted structured methodologies for mitigating **null values and data sparsity**.
- **Optimised Query Performance:** The strategic use of **Parquet format** resulted in significant **storage compression and accelerated query execution times**.

## **Future Enhancements**
- **Advanced Data Visualisation:** Develop interactive, dynamic dashboards for **enhanced interpretability** of attendance trends.
- **Pipeline Automation:** Integrate **Apache Airflow** for **automated scheduling and execution of data transformation workflows**.
- **Anomaly Detection Systems:** Implement **machine learning-based anomaly detection** to identify and address irregularities in attendance patterns.

## **Repository Structure**
```
📂 data-engineering-project
│── 📂 data  # Raw and transformed datasets (CSV/Parquet)
│── 📂 notebooks  # Jupyter Notebooks for exploratory analysis
│── 📂 scripts  # PySpark scripts for pipeline execution
│── README.md  # Detailed technical documentation
```

## **Author**
**Saqib Safdar**  
🌍 GitHub: [[Your GitHub Profile]](https://github.com/saqibsafdar11)



