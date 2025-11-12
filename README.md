# Databricks Metrics Catalog Demo

End-to-end set of Databricks notebooks that simulate a startup ERP system, enforce GDPR in the medallion architecture, and publish growth-focused gold metrics.

## Repository Structure

- `notebooks/01_generate_erp_startup_dataset.ipynb` – seeds synthetic ERP data straight into Spark DataFrames and derives monthly growth inputs.
- `notebooks/02_bronze_to_silver_gdpr.ipynb` – demonstrates GDPR-safe transformation from bronze to silver, including pseudonymisation and audit logging.
- `notebooks/03_silver_to_gold_metrics.ipynb` – aggregates curated silver data into gold-layer growth metrics and registers a reusable metrics view.

Run the notebooks sequentially for the best experience.

## Prerequisites

1. **Databricks Community Edition (free)** account. Sign up at [https://community.cloud.databricks.com](https://community.cloud.databricks.com) if you do not already have one.
2. **Workspace permissions** to create clusters and upload notebooks (default for Community Edition users).
3. Optional: `git` (if you plan to clone this repo locally before uploading notebooks).

## Quick Start: Deploy on Databricks Community Edition

1. **Clone or fork this repository.**
   ```bash
   git clone https://github.com/<your-org>/databricks-metrics-catalog.git
   cd databricks-metrics-catalog
   ```
   Alternatively, fork it to your own GitHub account so you can connect Databricks Repos directly.

2. **Log in to Databricks Free Edition.**
   - Go to [https://www.databricks.com/learn/free-edition](https://www.databricks.com/learn/free-edition).
   - Enter your credentials to access your personal workspace.

3. **Add the Git Folder**
   - In the left sidebar, click **Workspace**.
   - Go to Create - Git Folder
   - Paste this repository URL 

4. **Attach a cluster.**
   - Start (or reuse) the Community Edition single-node cluster.
   - Attach it to each notebook before running the cells. All required libraries ship with the runtime (only `numpy` is used in Python loops).

5. **Run the notebooks in order.**
   - Navigate to `Repos/<username>/databricks-metrics-catalog/notebooks/`.
   - **Step 1:** Open `01_generate_erp_startup_dataset` and execute the cells top-to-bottom to create the synthetic ERP catalog and growth baseline.
   - **Step 2:** Continue with `02_bronze_to_silver_gdpr`. This notebook:
     - Simulates bronze-layer raw data with PII.
     - Applies GDPR controls (consent filtering, pseudonymisation, purpose-limited marketing opt-ins).
     - Logs governance actions and performs compliance assertions.
     - Includes an optional final cell to write the silver dataset to the catalog (`erp_demo.silver_orders`).
   - **Step 3:** Finish with `03_silver_to_gold_metrics`. It:
     - Loads the silver dataset (from Unity Catalog or seeds a sample into Spark if nothing exists).
     - Computes monthly growth KPIs (revenue, retention, CAC, LTV, burn multiple, etc.).
     - Runs quality checks before materializing a gold table (`erp_demo.gold_growth_metrics`) and a companion metrics view.

6. **Verify outputs.**
   - Use DataFrames rendered in each notebook to confirm synthetic data and metrics look correct.
   - If you executed the persistence cells, open **Catalog ➜ erp_demo** (or use SQL) to inspect the `silver_orders`, `gold_growth_metrics`, and `gold_growth_metrics_view` objects.

## Optional: Local Execution without Databricks

You can run the notebooks locally with Jupyter or VS Code using Python 3.10+ and PySpark:
1. Create a virtual environment and install dependencies.
   ```bash
   python -m venv .venv
   source .venv/bin/activate  # or .venv\Scripts\activate on Windows
   pip install pyspark numpy
   ```
2. Launch Jupyter Lab/Notebook or VS Code, ensure a Spark session is created (the notebooks attempt to start one automatically), and run the cells. Persistence steps are skipped when Spark tables cannot be reached.

## Troubleshooting

- **Cluster won’t start:** Ensure you’re using the default Community Edition configuration; restarting or recreating the cluster usually resolves issues.
- **Import errors:** If an upload fails, try importing the notebook via URL (e.g., host the file on GitHub and paste the raw URL in Databricks’ import dialog).
- **Unity Catalog permissions:** Community Edition uses the legacy workspace filesystem. The notebooks still work; the optional write cells will save tables in the `erp_demo` database within the workspace metastore.
- **Execution order:** Run Step 01 ➜ 02 ➜ 03. Skipping steps may lead to missing tables or data.

## Next Steps

- Extend the dataset with additional facts (e.g., support tickets, usage events).
- Wire the gold metrics into dashboards (Databricks SQL, Power BI, etc.).
- Replace dummy hashing with your organisation’s key management solution when moving beyond demos.

Enjoy exploring the Databricks medallion architecture with a compliance-first mindset!

