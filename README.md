# AI Job Lead Generator (ADK + DataForSEO)

This project demonstrates a robust, multi-agent pipeline for reliable, high-volume job lead generation. It is built on the Google Agent Development Kit (ADK) and utilizes the DataForSEO Google Jobs API to bypass unstable web scraping, ensuring clean, structured, and real-time job data.

## Features

* **Asynchronous API Handling:** Manages the two-step (POST/GET) process required by high-volume SERP APIs.
* **Structured Data Output:** Directly consumes clean JSON data from Google Jobs, eliminating parsing errors common in web scraping.
* **End-to-End Automation:** Automatically submits the query, waits for the results, cleans the data, and exports the final output to a CSV file.
* **Secure Credential Management:** Utilizes the Kaggle Secrets utility to store sensitive API keys (Gemini, DataForSEO) securely.

##  Architecture

The system is orchestrated by a **Sequential Agent** to enforce the necessary dependency chain for the asynchronous API call:

1.  **Submission:** `submit_data_task_agent` (POST Request)
2.  **Retrieval:** `retrieve_data_agent` (Handles wait and GET Request)
3.  **Cleaning:** `format_agent` (LLM parses complex JSON)
4.  **Export:** `export_agent` (Converts clean JSON to CSV)




You must define the following environment variables in your Kaggle Secrets panel:

| Name | Value |
| :--- | :--- |
| `GOOGLE_API_KEY` | Your Gemini API Key |
| `DFSEO_LOGIN` | Your DataForSEO API Login (Email) |
| `DFSEO_PASSWORD` | Your DataForSEO API Password |



### 1. Install Dependencies

Ensure your notebook environment has the necessary libraries:

