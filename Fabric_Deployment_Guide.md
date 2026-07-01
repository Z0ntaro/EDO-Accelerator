# Microsoft Fabric Deployment Guide: EDO Accelerator

This guide provides step-by-step instructions to deploy, configure, and run the **Enterprise Data Observability (EDO) Accelerator** in your Microsoft Fabric environment.

---

## Prerequisites

Before starting, ensure you have:
1. A **Microsoft Fabric** capacity (or active Trial capacity).
2. A workspace created in Fabric with Fabric capacity turned on.
3. **Power BI Desktop** installed (for modifying or publishing the dashboard).
4. Access to the files in this repository.

---

## Step 1: Create the Fabric Workspace and Lakehouse

1. Log in to [Microsoft Fabric](https://app.fabric.microsoft.com/).
2. Navigate to **Workspaces** $\rightarrow$ **New Workspace**.
   * **Name**: `Sales_Intelligence` (or a name of your choice).
   * Under **Advanced**, ensure a Fabric/Trial capacity is selected.
3. Switch the experience to **Data Engineering** (using the selector at the bottom left).
4. Click **Lakehouse** to create a new Lakehouse.
   * **Name**: `Sales_Intelligence_LH`.
   * Click **Create**.

---

## Step 2: Upload Sample Datasets

The EDO Accelerator requires source Excel datasets to run.
1. Inside your new Lakehouse (`Sales_Intelligence_LH`), locate the **Files** section in the Explorer panel on the left.
2. Click on the ellipsis `...` next to **Files** and select **Upload** $\rightarrow$ **Upload Files**.
3. Upload the three sample Excel datasets from this repository:
   * `Customer_Master_Data.xlsx`
   * `Global_Climate_Records.xlsx`
   * `Retail_Observability_Intelligence.xlsx`

---

## Step 3: Import and Configure Notebooks

You need to import the 4 PySpark notebooks into your workspace.
1. Navigate back to the workspace homepage (`Sales_Intelligence`).
2. Click **Import** $\rightarrow$ **Notebook** $\rightarrow$ **From file**.
3. Upload the following notebooks from the repository:
   * `Register_Datasets.ipynb`
   * `Bronze to Silver.ipynb`
   * `Silver to Gold.ipynb`
   * `EDO_Validation_Engine.ipynb`
4. Once imported, open **each notebook** and configure their default Lakehouse connection:
   * In the top menu, verify if a default Lakehouse is selected.
   * If not, click **Add Lakehouse** $\rightarrow$ **Existing Lakehouse**, select `Sales_Intelligence_LH`, and click **Add**.

---

## Step 4: Import and Set Up the Data Pipeline

1. Go to the workspace homepage.
2. Click **Import** $\rightarrow$ **Data pipeline** $\rightarrow$ **From file**.
3. Upload the pipeline configuration file:
   * `EDO_Observability_Pipeline/EDO_Observability_Pipeline.json`
4. Open the newly imported data pipeline `EDO_Observability_Pipeline`.
5. **Link the Pipeline Activities to your Notebooks**:
   * Select the **Register Datasets Notebook** activity. In the settings tab below, select `Register_Datasets` from the notebook dropdown.
   * Select the **ForEach1** activity, and under **Settings**, click **Edit** (the pencil icon) to view inner activities:
     1. In **Notebook_Bronze_to_Silver**, set the notebook to `Bronze to Silver`.
     2. In **Notebook_Silver_to_Gold**, set the notebook to `Silver to Gold`.
     3. In **Notebook_EDO_Validation_Engine**, set the notebook to `EDO_Validation_Engine`.
   * Click **Save** in the top menu to lock in the configurations.

---

## Step 5: Run the Pipeline

1. In the pipeline editor, click **Run** (or select **Trigger Now**) in the top menu.
2. Monitor execution status:
   * The pipeline will first run `Register Datasets` which registers the uploaded files into the `dataset_registry` table.
   * It will then query `dataset_registry` and loop through each file sequentially, running ingestion, cleaning, KPI grouping, and validation rule testing.
3. Once completed, navigate to your Lakehouse SQL Endpoint or Lakehouse view to check the populated tables:
   * `bronze_*` and `silver_*` tables for each dataset.
   * `gold_kpi_by_region` and `gold_observability_log`.

---

## Step 6: Connect the Power BI Dashboard

1. Open **Power BI Desktop**.
2. Open the file `EDO Observability Dashboard v2.pbix` from this repository.
3. Update the data source connections to point to your Fabric Lakehouse SQL Endpoint:
   * In Fabric, open the `Sales_Intelligence_LH` **SQL Endpoint** page.
   * Copy the **SQL Connection String** from the settings pane (top right).
   * In Power BI Desktop, click **Transform Data** $\rightarrow$ **Data source settings**.
   * Change the server URL/endpoint to your copied SQL connection string.
   * Enter your credentials (Microsoft Account) when prompted.
4. Refresh the dashboard to load your live runs and observability scores!
5. Publish the dashboard back to your workspace (`Sales_Intelligence`) to share it with your team.
