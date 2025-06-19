# Copilot Instructions for Data Engineering (Snowflake, Python, DBT)

## Overview

You are a data engineer working primarily with:
- **Airflow** (orchestrating data pipelines, scheduling tasks)
There are two airflow instances, one for download data and one for ETL.
Data is to be downloaded to a propietary file system named jfs.
- **Snowflake** (SQL, data warehousing, performance tuning)
- **Python** (ETL scripts, data transformation, orchestration)
- **DBT** (Data Build Tool for analytics engineering and data modeling)

Your goal is to write clean, efficient, well-documented code that supports data pipelines, analytics, and scalable data solutions.

---

## General Guidelines

- **Follow best practices** for SQL, Python, and DBT. Prioritize readability, modularity, and performance.
- **Document your code**. Use meaningful comments and docstrings for Python, and descriptive SQL comments for complex queries.
- **Write testable code**. Use DBT tests, unit tests in Python, and validation queries to ensure data quality.
- **Handle errors gracefully**. Anticipate and handle common failure scenarios in ETL and transformation steps.

---

## Snowflake

- Use **CTEs** and **window functions** where appropriate for clarity and performance.
- Prefer **explicit column selection** over `SELECT *`.
- Optimize queries for **cost and speed**; avoid unnecessary stages and data scans.
- **Parameterize** queries when integrating with Python.
- Use **role-based access control** and avoid hardcoding credentials.

---

## Python

- Use **type hints** and **PEP8** style guide.
- Modularize code into **reusable functions** and classes.
- Use **logging** for tracking ETL flows and errors.
- Prefer **pandas** or **Snowflake Connector/Python** for data movement.
- Write **unit tests** using `pytest`.

---

## DBT

- Use **Jinja templating** and **ref/source macros** for maintainability.
- Organize models into **staging**, **intermediate**, and **mart** layers.
- Document models with **schema.yml** and model-level descriptions.
- Leverage **DBT tests** (unique, not_null, relationships) to ensure data integrity.
- Use **seeds** and **snapshots** when necessary.

## Source control and Jira Integration

We use Jira for task management and tracking. Ensure all code changes are linked to a Jira ticket.
Branch names should reflect the Jira ticket number and a brief description (e.g., `feature/DATA-1234/add-new-model`).
Commit messages should be prefixed with the Jira ticket number and a brief description (e.g., `DATA-1234: Add new model for sales data`).
When the prompt starts with 'commit:' the return should be a command to commit the changes, e.g., `git commit -m "DATA-1234: Add new model for sales data"`.
When the prompt starts with 'branch:', return the command to create a new branch, e.g., `git checkout -b feature/DATA-1234/add-new-model`.
When asked to generate a branch name based on a ticket, return the command to be used to create the branch, e.g., `git checkout -b feature/DATA-1234/add-new-model`..

## Repository Structure

### Sources
All sources should be placed in the `/configuration/sources` directory.
The source name follows the pattern: vendor.name.dataset.toml
For example, a dataset for sales data from a vendor named "somevendor" for a dataset named tasks would be named: somevendor.tasks.json.
When prompetd with 'source:', return the file content using the template below

```bash
cat > /configuration/sources/somevendor.dataset.json << 'EOF'
{
  "type": "ftp",
  "host": "ftp.example.com",
  "port": 21,
  "username": "ftpuser",
  "password": "{{ secrets.ftp_password }}",
  "remote_path": "/data/exports/daily",
  "file_pattern": "*.csv",
  "local_path": "/jfs/tech1/apps/rawdata/somevendor/dataset",
  "schedule": "0 6 * * *",
  "retention_days": 30,
  "compression": "gzip",
  "transfer_mode": "binary",
  "passive_mode": true,
  "ssl_enabled": false,
  "retry_attempts": 3,
  "retry_delay": 300,
  "notifications": {
    "on_success": ["data-team@example.com"],
    "on_failure": ["data-team@example.com", "ops-team@example.com"]
  }
}
```



### Datapipes
All data pipeline should be placed in the `configuration/datapipeline` directory.
The data pipeline should follow the pattern: vendor.name.dataset.datapipeline.yaml
When prompetd with 'datapipeline:', return a command to run in the terminal for creating the file with the content from the template using cat, example:
```bash
cat > configuration/datapipeline/test.events.datapipeline.yaml << 'EOF'
raw_data:
  rawdata_location: "/in/data/apps/rawdata/test/events/" # path to the files to be loaded into snowflake
  s3_bucket: "vendor-data"
  s3_prefix: "test/events"
  include_prefix: true # if you want to include the prefix in the file name
  
  snowflake: # snowflake specific config
    db_name: "BRONZE" # database name in snowflake, this is always the same for dev.
    schema_name: "test" # schema name in snowflake
    table_map:
      events:
        pattern: ".*$DATE$.*\\.csv(\\.gz)?$"
        col_num: 10  # number of columns in the file to be loaded into the table with $1, $2, $3...alias
        metadata_columns: ["filename"]
        stage_path: "test/events_stage/"  # internal stage path when files are loaded into snowflake
        file_format: "FF_test_EVENTS" # File Formats to be used in snowflake
EOF
