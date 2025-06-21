# Modern Data Warehouse ELT Pipeline with dbt & Snowflake

## Overview

This project demonstrates the design and implementation of a robust, modern ELT (Extract, Load, Transform) data pipeline, leveraging the power of a cloud-native data warehouse (Snowflake) and a transformative data modeling tool (dbt - Data Build Tool). The focus is on loading raw data directly into the warehouse and then performing all transformations within Snowflake using SQL, emphasizing data quality, automated testing, and continuous integration/continuous delivery (CI/CD) for data assets.

This architecture offers unparalleled scalability, high performance, significantly reduced maintenance overhead, and a strong emphasis on data governance, reliability, and agile data development.
---
## Problem Solved

Traditional ETL processes often involve complex, hard-to-maintain code outside the data warehouse, leading to slower development cycles, limited scalability, and inherent difficulties in ensuring consistent data quality and proper governance. This project directly addresses these challenges by:
* Shifting all data transformation logic into the powerful **cloud data warehouse (Snowflake)**, fully leveraging its massive compute capabilities and optimizing resource utilization.
* Enabling **SQL-first, version-controlled transformations** with dbt, which makes all business logic transparent, easily auditable, and rigorously testable.
* Implementing **automated testing and CI/CD practices** for data transformations, aligning data engineering workflows with modern software development principles.
* Significantly improving **data reliability and trustworthiness** through comprehensive, automated testing and auto-generated, up-to-date documentation.
* Accelerating the **delivery of clean, analytics-ready data** to business users and downstream applications, fostering faster, more informed decision-making.

## High-Level Architecture

```mermaid
graph TD
    subgraph Ingestion
        A[Data Sources] --> B[Fivetran / Airbyte<br>(or Custom Ingestion)];
    end

    B --> C[Snowflake (Raw Layer)];

    subgraph Transformation & Modeling
        C --> D[dbt Transformation Layer];
        D --> E[Snowflake (Data Marts)];
    end

    E --> F[BI Tool (e.g., Tableau, Power BI)];

    subgraph CI/CD
        G[GitHub (dbt Code)] -- Trigger --> H[GitHub Actions Workflow];
        H -- Deploys & Tests --> D;
    end

    style A fill:#f9f,stroke:#333,stroke-width:2px
    style B fill:#bbf,stroke:#333,stroke-width:2px
    style C fill:#ccf,stroke:#333,stroke-width:2px
    style D fill:#ddf,stroke:#333,stroke-width:2px
    style E fill:#eef,stroke:#333,stroke-width:2px
    style F fill:#dfb,stroke:#333,stroke-width:2px
    style G fill:#bfb,stroke:#333,stroke-width:2px
    style H fill:#fdb,stroke:#333,stroke-width:2px

```
## Technologies Used

* **Cloud Data Warehouse:**
    * **Snowflake:** Utilized as the central, highly scalable, and performant cloud-agnostic data warehouse.
* **Data Transformation & Modeling:**
    * **dbt (Data Build Tool):** Employed for defining, managing, testing, and documenting all data transformations using modular SQL.
* **Data Ingestion (EL):**
    * **Fivetran / Airbyte:** (Conceptual integration) Representing automated data connectors for efficient loading from diverse SaaS applications and databases.
    * **Custom Python Scripts:** Developed for bespoke ingestion from specific APIs or file-based sources into Snowflake.
* **Version Control:**
    * **Git / GitHub:** Essential for source code management of the dbt project, facilitating collaborative development and code reviews.
* **CI/CD Automation:**
    * **GitHub Actions:** Configured to automate testing, compilation, and deployment of dbt models upon code changes, ensuring a continuous integration and delivery pipeline.
* **Programming Languages:**
    * **SQL (Advanced):** The primary language for building robust and performant dbt models and transformations within Snowflake.
    * **Python:** Used for developing custom data ingestion scripts and any auxiliary utilities.
* **Business Intelligence (BI) Tools:**
    * **Tableau / Power BI / Looker:** (Conceptual integration) Representing visualization tools connected to the Snowflake data marts for generating interactive dashboards and reports.

---

## Project Phases & Implementation Details

### Phase 1: Data Ingestion & Raw Layer in Snowflake (EL)

* **Objective:** To establish a robust mechanism for extracting and loading raw data from various sources directly into Snowflake's raw layer, preserving its original structure.
* **Implementation:**
    * Selected and integrated a public transactional dataset (e.g., simulated e-commerce orders, customer interactions, or sales data).
    * Implemented data loading into a dedicated `RAW` schema within Snowflake. This was achieved either by simulating automated data connectors (like Fivetran/Airbyte) or by developing bespoke Python scripts utilizing the Snowflake Connector for Python to ingest data directly from flat files (CSV, JSON) or APIs.
    * Ensured that all raw data was loaded "as-is," allowing for historical fidelity and the flexibility for future reprocessing or new transformation requirements.

### Phase 2: dbt Project Setup & Core Transformations

* **Objective:** To define, build, and manage all data transformations and modeling within Snowflake using dbt.
* **Implementation:**
    * Initialized a new dbt project and meticulously configured its connection to the Snowflake data warehouse, including managing separate profiles for development and production environments.
    * Designed and implemented a comprehensive dimensional data model (following a star schema approach) to organize the raw, disparate data into logical, analytical structures (e.g., `fact_orders`, `dim_customers`, `dim_products`).
    * Developed layered dbt models using advanced SQL:
        * **Staging Layer:** Focus on initial data cleansing, type casting, basic standardization, and deduplication from the raw tables.
        * **Intermediate Layer:** Perform complex joins, aggregations, and business logic calculations to prepare data for final consumption.
        * **Mart Layer:** Create highly optimized, analytics-ready tables designed for specific business use cases and direct consumption by BI tools.
    * Utilized dbt's Jinja templating and macros extensively for code reusability and parameterization, enhancing modularity and maintainability.
    * Implemented incremental models where applicable to optimize refresh times and reduce compute costs.

### Phase 3: Data Quality Testing & Documentation with dbt

* **Objective:** To guarantee the accuracy, consistency, and understandability of all transformed data assets.
* **Implementation:**
    * Implemented a comprehensive suite of dbt tests at various stages of the pipeline. This included generic tests (`not_null`, `unique`, `accepted_values`, `relationships`) and custom SQL tests tailored to specific business rules and data constraints.
    * Thoroughly documented all dbt models, sources, and individual columns using dbt documentation capabilities, generating a browsable data catalog that serves as a single source of truth for data consumers.
    * Conducted regular data profiling to proactively identify data characteristics, distributions, and potential anomalies, informing subsequent data quality improvements.

### Phase 4: CI/CD Implementation (GitHub Actions)

* **Objective:** To automate the testing, validation, and deployment of dbt code changes, ensuring a continuous and reliable delivery pipeline for data assets.
* **Implementation:**
    * Configured GitHub Actions workflows to integrate with the dbt project.
    * Automated `dbt deps`, `dbt compile`, and `dbt test` runs on every pull request to ensure code syntax, model dependencies, and data integrity are validated before merging.
    * Implemented an automated deployment step (running `dbt build` or `dbt run && dbt test`) triggered on merges to the `main` branch, pushing validated transformations to the production Snowflake environment.
    * Managed Snowflake connection credentials securely using GitHub Secrets, ensuring sensitive information is never exposed in the codebase.
    * This setup significantly enhanced the agility of data development, reduced manual errors, and provided rapid feedback on changes.
 
---

## How to Run This Project (Local Development & Deployment)

### Prerequisites
* An active Snowflake account (trial or standard)
* dbt Core installed (`pip install dbt-snowflake`)
* Python 3.x
* Git and a GitHub account
* (For full pipeline execution) Basic understanding of AWS CLI and IAM if extending to custom ingestion.

### Local Setup
1.  **Clone the repository:**
    ```bash
    git clone [https://github.com/your-github/modern-data-warehouse-elt.git](https://github.com/your-github/modern-data-warehouse-elt.git)
    ```
2.  **Navigate to dbt project directory:**
    ```bash
    cd modern-data-warehouse-elt/dbt_project_name
    ```
    (Replace `dbt_project_name` with the actual folder name of your dbt project)
3.  **Configure Snowflake connection:**
    * Create a `profiles.yml` file in your `~/.dbt/` directory.
    * Populate `profiles.yml` with your Snowflake connection details, ensuring it matches the project name (e.g., `modern_dw_elt` if your dbt project is named `modern_dw_elt`):
        ```yaml
        modern_dw_elt: # This should match your dbt project name in dbt_project.yml
          target: dev
          outputs:
            dev:
              type: snowflake
              account: your_snowflake_account_identifier # e.g., ab12345.us-east-1
              user: your_snowflake_user
              password: your_snowflake_password
              role: your_snowflake_role # e.g., SYSADMIN or a custom dbt role
              warehouse: your_snowflake_warehouse_name
              database: your_snowflake_database_name
              schema: dbt_dev_schema # Or your preferred development schema
              threads: 4
              client_session_keep_alive: False
        ```
4.  **Install dbt dependencies:**
    ```bash
    dbt deps
    ```
5.  **Test connection:**
    ```bash
    dbt debug
    ```
6.  **Run dbt models and tests locally:**
    * To build all models: `dbt run`
    * To run all tests: `dbt test`
    * To generate and serve documentation locally: `dbt docs generate && dbt docs serve`

### CI/CD Deployment (GitHub Actions Workflow)
1.  **GitHub Repository Setup:** Ensure your dbt project code is pushed to a GitHub repository.
2.  **Secure Credentials:** Create GitHub Secrets in your repository settings for sensitive Snowflake credentials (e.g., `SNOWFLAKE_ACCOUNT`, `SNOWFLAKE_USER`, `SNOWFLAKE_PASSWORD`, `SNOWFLAKE_ROLE`, `SNOWFLAKE_WAREHOUSE`, `SNOWFLAKE_DATABASE`).
3.  **Configure GitHub Actions Workflow:** Create a YAML file (e.g., `dbt_ci.yml`) in your repository's `.github/workflows/` directory with the following structure:
    ```yaml
    name: dbt CI/CD

    on:
      push:
        branches:
          - main
      pull_request:
        branches:
          - main

    jobs:
      dbt-build-test:
        runs-on: ubuntu-latest
        steps:
        - name: Checkout code
          uses: actions/checkout@v4

        - name: Set up Python
          uses: actions/setup-python@v5
          with:
            python-version: '3.9' # Or your dbt's supported Python version

        - name: Install dbt and dependencies
          run: |
            pip install dbt-snowflake~=1.7.0 # Pin to your dbt version
            pip install -r requirements.txt # If you have other Python dependencies

        - name: Create dbt profiles.yml
          run: |
            mkdir -p ~/.dbt
            echo "modern_dw_elt:" > ~/.dbt/profiles.yml
            echo "  target: github_ci" >> ~/.dbt/profiles.yml
            echo "  outputs:" >> ~/.dbt/profiles.yml
            echo "    github_ci:" >> ~/.dbt/profiles.yml
            echo "      type: snowflake" >> ~/.dbt/profiles.yml
            echo "      account: ${{ secrets.SNOWFLAKE_ACCOUNT }}" >> ~/.dbt/profiles.yml
            echo "      user: ${{ secrets.SNOWFLAKE_USER }}" >> ~/.dbt/profiles.yml
            echo "      password: ${{ secrets.SNOWFLAKE_PASSWORD }}" >> ~/.dbt/profiles.yml
            echo "      role: ${{ secrets.SNOWFLAKE_ROLE }}" >> ~/.dbt/profiles.yml
            echo "      warehouse: ${{ secrets.SNOWFLAKE_WAREHOUSE }}" >> ~/.dbt/profiles.yml
            echo "      database: ${{ secrets.SNOWFLAKE_DATABASE }}" >> ~/.dbt/profiles.yml
            echo "      schema: github_actions_schema # Use a dedicated schema for CI/CD" >> ~/.dbt/profiles.yml
            echo "      threads: 1" >> ~/.dbt/profiles.yml
            echo "      client_session_keep_alive: False" >> ~/.dbt/profiles.yml
          env:
            SNOWFLAKE_ACCOUNT: ${{ secrets.SNOWFLAKE_ACCOUNT }}
            SNOWFLAKE_USER: ${{ secrets.SNOWFLAKE_USER }}
            SNOWFLAKE_PASSWORD: ${{ secrets.SNOWFLAKE_PASSWORD }}
            SNOWFLAKE_ROLE: ${{ secrets.SNOWFLAKE_ROLE }}
            SNOWFLAKE_WAREHOUSE: ${{ secrets.SNOWFLAKE_WAREHOUSE }}
            SNOWFLAKE_DATABASE: ${{ secrets.SNOWFLAKE_DATABASE }}

        - name: Run dbt build and tests
          run: |
            cd dbt_project_name # Navigate to your dbt project folder
            dbt build --target github_ci # Build and test models
            dbt test --target github_ci # Explicitly run tests again if needed
    ```
    This workflow will automatically execute `dbt build` and `dbt test` whenever code is pushed to `main` or a pull request is opened, ensuring continuous validation of your data pipeline.
---
## Future Enhancements
* Implement advanced **data observability** features using tools like Monte Carlo or Soda, integrating anomaly detection and lineage tracking.
* Develop a robust **data governance framework** on top of Snowflake using features like Tagging, Masking Policies, and Row Access Policies.
* Explore integrating **streaming data ingestion** into Snowflake using Snowpipe for near real-time data availability.
* Create a dedicated **BI dashboard** in Tableau, Power BI, or Looker that directly consumes the dbt-generated data marts, providing interactive and timely business insights.
* Extend the project to include **data sharing capabilities** using Snowflake Data Sharing for secure collaboration with external partners or internal departments.
* Implement **cost optimization strategies** within Snowflake by fine-tuning warehouse sizes and suspension policies based on usage patterns.

## Contact
Feel free to reach out with any questions or feedback!
* **Subash Yadav:** yadavsubash0123@gmail.com
* **LinkedIn:** [https://www.linkedin.com/in/mathachew7/]
* **GitHub:** [github.com/mathachew7]

---
**Push to GitHub is still remaining!**
