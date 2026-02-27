📘 Pipeline Description
Pipeline Name: google-news-ingestion
Platform: Microsoft Fabric – Data Engineering (Pipeline)
This pipeline automates the end‑to‑end flow from API ingestion to reporting, with daily scheduling and email alerting on key conditions.

🎯 Purpose

Call an external REST API to fetch the latest news data (JSON).
Land raw JSON to Lakehouse (Bronze).
Run Notebook 1(data transformation) to convert JSON → tabular Silver (incremental).
Run Notebook 2(sentiment analysis) to add ML sentiment and write to Gold (incremental).
Serve Power BI from Gold tables.


🧩 Activities & Flow


Copy Data — API → Lakehouse (Bronze)

Source: HTTP (REST API, GET)
Destination: Lakehouse / Files / news/latest_news/ (JSON)
Behavior: Append using SQL MERGE



Notebook Activity — JSON → Silver (Incremental)

Notebook: 01_json_to_silver_transformation.ipynb
Logic:

Read raw JSON from Bronze
Normalize & flatten nested fields
Cast schema / select business columns
Incremental load
Merge/upsert into silver.news_data (Delta)





Notebook Activity — Sentiment → Gold (Incremental)

Notebook: 02_sentiment_analysis_and_gold_load.ipynb
Logic:

Read new/changed rows from silver.news_data
Apply sentiment model → sentiment_label
Incremental merge into gold.news_with_sentiment





(Downstream) Power BI

Power BI: Connects to gold.news_with_sentiment_data




⏰ Scheduling

Schedule: Every day at 10:00 AM (IST)
Behavior: Sequential orchestration (Copy Data → NB1 → NB2)
