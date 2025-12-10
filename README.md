# AI Job Lead Generator (ADK + DataForSEO)

This project utilizes the Google Agent Development Kit (ADK) and the Gemini API to create a reliable and well-structured data pipeline. It automatically searches for job listings via a third-party SERP API, retrieves the data asynchronously, and processes the results with custom logic for high-quality, sorted output.

# Key Features & Technical Highlights
Custom Deterministic Sorting: Implements a robust Python tool function (sort_job_listings) to normalize the mixed units in the time_ago field (hours, days, weeks) into a single comparable numeric key. This ensures listings are always accurately sorted from Newest to Oldest.

LLM Agent Orchestration: Uses a Sequential Agent (root_orchestrator) to manage the workflow across five distinct processing stages.

Structured Data Output: The pipeline enforces a clean output structure, extracting key fields (title, employer_name, location, source_url, time_ago) and exporting the final, clean, and sorted data to a .csv file.

Asynchronous API Handling: Manages the interaction with the external DataForSEO API using the Task POST/GET pattern, demonstrating competence in handling non-instantaneous data retrieval.

Tool-Centric Design: Offloads complex and repetitive data manipulation tasks (filtering, sorting, CSV export) from the large language model's context window to dedicated, reliable Python tool functions.

# Architecture and Execution Flow
The workflow is managed by the sequential root_orchestrator, ensuring reliable, step-by-step execution:

Agent Flow
submit_agent: Sends the search query (e.g., "Software Engineer") to the DataForSEO API and extracts the unique task_id.

retrieve_agent: Uses the task_id to retrieve the full raw job search results JSON after a designated wait time. (Note: In a production environment, this step would be enhanced with non-blocking polling.)

format_agent: Finds the job list inside the raw API JSON and extracts clean fields, filtering out jobs older than 7 days.

sort_agent: Calls the custom sort_job_listings tool to process the list and ensure it is organized by posting time (newest first).

export_agent: Takes the final, clean, and sorted JSON list and saves it to final_software_engineer_jobs.csv using the export_to_csv tool.



You must define the following environment variables in your Kaggle Secrets panel:

`GOOGLE_API_KEY`
`DFSEO_LOGIN` - Your DataForSEO API Login (Email) 
`DFSEO_PASSWORD`- Your DataForSEO API Password 



### 1. Install Dependencies

Ensure your notebook environment has the necessary libraries:

