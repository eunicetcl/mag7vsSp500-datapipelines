diff --git a/README.md b/README.md
index 9130c52099eb991ed98abb137930caae3f89b798..461586ddc2d4191d902730483e0d43c664b1250f 100644
--- a/README.md
+++ b/README.md
@@ -1,44 +1,70 @@
-
-## Conceptual Knowledge about GIT (15 mins)
-
-### What is GIT?
-
-Review this [video](https://youtu.be/2ReR1YJrNOM) in class.
-
-Check-in with learners for a short Q&A about the video.
-
----
-
-
-### The difference between FORK and CLONE
-
-<img src="./assets/clone-vs-fork.png" />
-
-When you fork, you are copying someone else's remote repository to your remote repository.
-
-When you clone, you are copying a remote repository to your local machine (laptop).
-
----
-
-## Part 2 - Push Changes. (20 mins)
-
-Follow these steps to try making changes and push them to the remote repository. 
-
-Step 1: Create a new file `test.txt` in the cloned repository (folder). 
-
-Step 2: Open the file and simply type "testing 123" in it.
-
-Step 3: Open up the Terminal and ensure that you are in the directory of the cloned repository (folder) by using `pwd` command.
-
-Step 4: Stage the changed file by typing `git add .`
-
-Step 5: Commit it with a simple message `git commit -m "made changes"`.
-
-Step 6: Push the committed change to remote repository `git push origin main`.
-
-Step 7: Visit your remote repository at github.com to see the changes. 
-
-The remote repository URL is `https://github.com/<your username>/3m-data-assignment-1.1`.
-
-
----
+# MAG7 Data Pipelines
+
+This project ingests and models market data for the "Magnificent Seven" equities plus key benchmarks, sentiments, and news. It uses Meltano to extract and load raw data into BigQuery, Dagster to orchestrate and validate runs (including Great Expectations), and dbt to build curated star-schema marts for analytics.
+
+## How to run
+
+### 1) Connect to Google Cloud / BigQuery
+- Create (or reuse) a GCP project and enable the **BigQuery API**.
+- Create a service account with **BigQuery Data Editor** + **BigQuery Job User** roles (and **Storage Object Viewer** if you use GCS-external tables).
+- Download the JSON key and point `GOOGLE_APPLICATION_CREDENTIALS` to it. Optionally run `gcloud auth application-default login` for user credentials.
+- Choose your target dataset and location (e.g., `market_intel_dev` in `US`) and set the environment variables in `.env` (see `configs/env/.env.example`).
+
+Example `.env` snippet:
+```bash
+BIGQUERY_PROJECT_ID=my-gcp-project
+BIGQUERY_DATASET=market_intel_dev
+BIGQUERY_LOCATION=US
+GOOGLE_APPLICATION_CREDENTIALS=/path/to/service_account.json
+```
+
+### 2) Set remaining environment variables
+Create a `.env` file (see `configs/env/.env.example`) with Meltano tap settings plus Dagster/Great Expectations configuration. Load it for each shell session:
+```bash
+export $(grep -v '^#' configs/env/.env.example | xargs)  # or use your own .env
+```
+
+### 3) Install dependencies
+Use Python 3.10+ and install project requirements:
+```bash
+python -m venv .venv
+source .venv/bin/activate
+pip install -r requirements.txt
+```
+
+### 4) Run Meltano locally (optional data load)
+Execute any extractor/loader job, for example prices to BigQuery:
+```bash
+meltano elt tap-yahoo-finance target-bigquery --job_id=prices_dev
+```
+
+### 5) Launch Dagster for orchestration
+Start the Dagster web UI to trigger scheduled or manual runs:
+```bash
+cd dagster_project
+export DAGSTER_HOME=$(pwd)/.dagster_home
+mkdir -p "$DAGSTER_HOME"
+dagster dev -h 0.0.0.0 -p 3000
+```
+Open http://localhost:3000 to run the Meltano-wrapping jobs and view Great Expectations results.
+
+### 6) Build analytics models with dbt
+From the `dbt/` directory, target your environment (dev/prod) and build the staging and mart models:
+```bash
+cd dbt
+dbt build --target dev
+```
+
+### 7) Validate with Great Expectations directly (optional)
+Run checkpoints against loaded tables:
+```bash
+cd great_expectations
+great_expectations checkpoint run prices_checkpoint
+```
+
+## Project layout (high level)
+- `configs/assets/` — shared ticker lists and configuration helpers.
+- `meltano.yml` — extract/load pipelines for prices, sentiment, and news.
+- `dagster_project/` — Dagster jobs, resources, schedules, and sensors wrapping Meltano and Great Expectations.
+- `dbt/` — staging and curated star-schema models with tests.
+- `great_expectations/` — expectation suites and checkpoints for data quality.
