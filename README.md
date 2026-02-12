# AI Job Sourcer (Google ADK + DataForSEO)

A multi-agent job search pipeline that fetches recent Software Engineer jobs from Google Jobs via DataForSEO, cleans and filters the results using Gemini, sorts them by recency, and exports the final list to a CSV.

## What This Project Does

The pipeline will:

Submit a Google Jobs search task to DataForSEO

Wait + retrieve the completed results

Extract + format clean job listings into a consistent JSON structure

Filter results to jobs posted within the last 7 days

Sort jobs by recency (time_ago)

Export the final list to a CSV file

Output:

A clean JSON list of {title, employer_name, location, source_url, time_ago}

A CSV file (e.g., final_software_engineer_jobs.csv)

### The agent handles a epeatable workflow that could:

Pull fresh listings programmatically

Convert large, nested API responses into clean structured data

Be easy to extend (more filters, multiple locations, more roles)

Use LLMs only where they add value (parsing + formatting)

## Architecture (How It Works)

Project uses Google ADK agents orchestrated in a SequentialAgent workflow:

1)Submit Agent (Tool Call)

Responsibility: Create a DataForSEO Google Jobs task.

- Calls submit_google_jobs_task(keyword, location_name)

- Extracts tasks[0].id (task ID) for the next step

Why a separate agent?
Keeps API submission deterministic and easy to debug.

2)Retrieve Agent (Tool Call)

Responsibility: Retrieve results using the task ID.

- Calls retrieve_google_jobs_results(task_id)

- Waits for processing (currently a fixed sleep)

Why separate retrieval?
DataForSEO is asynchronous: you submit first, then fetch results later.

3)Format Agent (LLM-only step)

Responsibility: Convert raw API response into a clean job list.

- Locates tasks[0].result[0].items

- Keeps items not older than 7 days

Outputs a normalized JSON list with only the fields you care about

Why use Gemini here?
The API response is deeply nested and inconsistent across entries. Gemini is used like a robust parser to extract and normalize the structure into a clean schema.

4)Sort Agent (Tool Call)

Responsibility: Sort jobs by recency.

- Calls sort_job_listings(json_list, sort_field="time_ago")

- Converts time_ago into a comparable numeric value (hours) and sorts

Why a deterministic tool instead of LLM sorting?
Sorting should be predictable and testable. This avoids “LLM creativity” in ordering.

5)Export Agent (Tool Call)

Responsibility: Save results as CSV.

- Calls export_to_csv(json_list, filename)

- Produces final_software_engineer_jobs.csv

Why export as CSV?
It’s easy to scan, share, and load into spreadsheets or a downstream analytics pipelin

## Key Features & Technical Highlights

Custom Deterministic Sorting: Implements a robust Python tool function (sort_job_listings) to normalize the mixed units in the time_ago field (hours, days, weeks) into a single comparable numeric key. This ensures listings are always accurately sorted from Newest to Oldest.

LLM Agent Orchestration: Uses a Sequential Agent (root_orchestrator) to manage the workflow across five distinct processing stages.

Structured Data Output: The pipeline enforces a clean output structure, extracting key fields (title, employer_name, location, source_url, time_ago) and exporting the final, clean, and sorted data to a .csv file.

Asynchronous API Handling: Manages the interaction with the external DataForSEO API using the Task POST/GET pattern, demonstrating competence in handling non-instantaneous data retrieval.

Tool-Centric Design: Offloads complex and repetitive data manipulation tasks (filtering, sorting, CSV export) from the large language model's context window to dedicated, reliable Python tool functions.

## Reliability Features

Gemini calls use HttpRetryOptions with exponential backoff.
LLM APIs can fail transiently (timeouts, rate limits). Retries make the pipeline much more stable without manual reruns.

LLM call budget
Prevents runaway multi-agent loops and controls cost.

Tool separation
Network calls (DataForSEO) are done through deterministic tools, while Gemini handles parsing/formatting.
This keeps the system reliable: tools do I/O; the model does interpretation.




You must define the following environment variables in your Kaggle Secrets panel:

`GOOGLE_API_KEY`
`DFSEO_LOGIN` - Your DataForSEO API Login (Email) 
`DFSEO_PASSWORD`- Your DataForSEO API Password 



Ensure your notebook environment has the necessary libraries:

