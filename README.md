
# End-to-End Retail Data Engineering Project: Shopping Mart Analytics

## 📌 Project Overview
This project demonstrates the design and implementation of an enterprise-grade, end-to-end data analytics platform for a mid-sized retail business ("Shopping Mart"). The objective is to combine structured transactional databases with unstructured social media and web tracking data to deliver actionable business insights regarding customer behavior, product sentiment, sales trends, and inventory management.

The system is built entirely within the **Microsoft Fabric** ecosystem, leveraging the **Medallion Architecture** to progressively refine raw data into high-quality, business-ready semantic models powering an interactive **Power BI** executive dashboard.

---

## 🏗️ Architecture & Data Flow
The architecture follows a modular, lakehouse-centric design using a Medallion pattern to separate concerns and guarantee data quality.


### 1. Ingestion Layer (Metadata-Driven Pipeline)
Instead of hardcoding individual ingestion routines, a **metadata-driven ingestion pipeline** was built using **Fabric Data Pipelines**. 
* **Source:** Structural transactional data (Orders, Customers, Products) and Unstructured text/log data (Product Reviews, Social Media Sentiment, Web Blogs) exposed via REST APIs.
* **Mechanism:** A control file (`metadata.json`) defines source paths, configurations, and target destinations. **Lookup** and **For-Each** activities read this configuration file dynamically to orchestrate multi-file ingestion loops. 

### 2. Storage & Transformation Layers (Medallion Pattern)
Data is logically isolated across three distinct **Fabric Lakehouses**:

* **Bronze Layer (Raw Storage Zone):** Acts as the historical data repository. Ingested `CSV` and `JSON` files are landed here verbatim to maintain an immutable source of truth for future auditing or pipeline reprocessing.
* **Silver Layer (Enriched & Validated Zone):** Powered by **PySpark Notebooks** running on a distributed Spark engine. The data undergoes **data cleansing and schema validation**:
    * Dropping irrelevant null values on key identifiers (`OrderID`, `CustomerID`).
    * Standardizing structural anomalies (casting string-based text fields into real `Date` or `Decimal` data types).
    * **Data Integration:** Executed an inner join between transactional fact logs and dimensional lookups. 
    * Data is written back out into highly compressed, open-source **Parquet** files.
* **Gold Layer (Curated Business Zone):** Dedicated to heavy analytical optimizations. **PySpark Aggregations** calculate critical business metrics:
    * Average product ratings from raw customer reviews.
    * Aggregated user engagement counts per webpage and distinct interaction types.
    * Platform-specific social media sentiment distributions.
    * Data is materialized as **Delta Lake Tables**, unlocking ACID transaction compliance and schema enforcement over data lake storage.

### 3. Serving Layer (Semantic Model & Visualization)
* **Direct Lake Mode:** The Power BI reporting layer connects directly to the Gold Delta Tables utilizing Microsoft Fabric’s **Direct Lake Mode**. This bypasses traditional data import (`Import Mode`) or slow querying (`DirectQuery`), reading raw Parquet files directly from OneLake with memory-resident performance.
* **Star Schema Modeling:** Designed a standard dimensional model incorporating a dedicated, programmatic **Date Table** to support comprehensive time-intelligence metrics.
* **Executive Dashboard:** A Power BI dashboard showcasing high-level KPIs:
    * Cross-filtering slicers (Year, Month, Product Category).
    * Top 5 rankings (Top products by sales value, top products by sentiment, top customers).
    * Time-series tracking (Sales trend by month to spot cyclical patterns or operational friction).

---

## 🛠️ Tech Stack & Key Concepts
* **Orchestration & ETL:** Microsoft Fabric Data Pipelines, Lookup/For-Each Control Loops.
* **Compute Engine:** Apache Spark (PySpark SQL Functions).
* **Storage Format:** Delta Lake / Parquet (OneLake).
* **Data Modeling:** Star Schema (Fact and Dimension Tables), Semantic Modeling.
* **Business Intelligence:** Power BI (Direct Lake connectivity, DAX, Interactive Filtering).
* **Design Pattern:** Metadata-Driven Architectures, Medallion Layout Framework.

---

## 🚀 Key Takeaways & Business Impact
1. **Scalability:** The framework is entirely configuration-driven. Adding 100 new source files requires changing a single entry in a JSON file—no new data pipelines need to be manually drafted.
2. **Performance:** By using Delta tables and Direct Lake connectivity, data fresh-to-report latency drops drastically while keeping cloud compute overhead down.
3. **Actionable Insights:** Correlating operational sales with unstructured customer reviews and website behavior allows marketing teams to immediately see where customers are dropping off in the buying funnel (e.g., viewing an item vs. adding it to a cart).

















































<img width="2060" height="1193" alt="image" src="https://github.com/user-attachments/assets/bfb6c3ae-caee-40e6-bb66-cd07f67b6a99" />
