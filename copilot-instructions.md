# Copilot Instructions for Data Engineering (Snowflake, Python, DBT)

## Overview

You are a data engineer working primarily with:
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

*These instructions help Copilot and contributors generate high-quality, context-appropriate suggestions for this repository.*
