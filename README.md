# RetailPulse: End-to-End Retail ETL & Analytics Engine
### *Turning Raw Transaction Chaos into Actionable Business Intelligence*

---

##  Executive Summary
**RetailPulse** is a robust, fault-tolerant Data Engineering pipeline designed to handle the complete lifecycle of retail transaction data. Built to solve the "Garbage In, Garbage Out" problem, it features a sophisticated ingestion layer that quarantines bad data without halting operations, a dynamic loyalty engine that calculates complex rewards in real-time, and an AI-driven lead generation module.

This solution was architected to demonstrate **Resilience, Scalability, and Business Impact**, processing 50,000+ simulated transactions to deliver clean, 3NF-normalized data ready for enterprise analytics.

---

##  Key Features (The "Why We Win")

### **Fault-Tolerant Data Ingestion (The "Air Lock")**
Most pipelines crash when they see bad data. Ours gets stronger.
* **Validation Logic:** Implements strict checks for "Ghost Stores," "Future Dates," and "Negative Prices."
* **The Quarantine Protocol:** Bad data is never deleted. It is serialized into JSON and stored in a `rejected_records` "Hospital Table" for audit and repair.
* **Orphan Prevention:** Automatically rejects line items if their parent receipt was flagged, ensuring 100% Referential Integrity.

### **Multi-Tier Loyalty Engine (The "Solver")**
We move beyond simple "1 point per $1" logic.
* **"Best Rule Applies" Algorithm:** The system simulates three parallel futures (Standard, Big Spender, Weekend Bonus) for every transaction and automatically awards the customer the *maximum* possible points.
* **Real-Time Accrual:** Points are immediately written back to the Customer Master profile.

### **Dynamic RFM Segmentation**
* **Self-Healing Thresholds:** Instead of static dollar values, we use **90th Percentile Dynamic Thresholding**. As inflation drives prices up, our "High Spender" definition automatically adjusts to remain statistically significant.
* **Churn Detection:** Automatically flags users with points who haven't visited in 30+ days as "At-Risk."

### **Bonus: AI Lead Generation**
* **The "Shadow" Database:** Captures phone numbers from "Guest" transactions.
* **Entity Resolution:** Deduplicates repeat guest visits into unique "Hot Leads," assigning an AI-simulated "Conversion Probability" score to prioritize sales calls.

---

## System Architecture

![System Architecture Overview](Python_ETL.png)

The solution follows a strict **Separation of Concerns** philosophy, decoupling Compute (Python) from Storage (SQL).

### **Phase 1: The Simulation (Data Factory)**
* **Tool:** `Faker`, `NumPy`
* **Role:** Generates 50,000 realistic transactions with a **Pareto Distribution** (80% Member / 20% Guest).
* **The Chaos Monkey:** Deliberately corrupts ~5% of data (Ghost Stores, Future Dates) to prove the validation layer works.

### **Phase 2: The ETL Pipeline**
* **Extract:** Reads raw CSVs into a Pandas Staging Area.
* **Transform:**
    * Casts types (String -> Date).
    * Validates business rules.
    * Splits flow into **Clean Stream** and **Rejected Stream**.
* **Load:** Persists clean data into 3NF-normalized SQLite tables.

### **Phase 3: The Consumption Layer**
* **Analytics:** SQL queries feed the Loyalty Engine.
* **Reporting:** Generates final CSV artifacts for "Ground Truth" verification.

### **Data Schema (Entity Relationship Diagram)**
![Entity Relationship Diagram](ER Diagram.png)

---

## Tech Stack

| Component | Technology | Reasoning |
| :--- | :--- | :--- |
| **Language** | Python 3.9+ | Best-in-class for vectorised data manipulation. |
| **Logic Core** | Pandas | C-speed processing for complex rules on large datasets. |
| **Database** | SQLite3 | Serverless, portable, ACID-compliant storage. Zero setup required for judges. |
| **Synthetic Data** | Faker | Generates realistic PII (Names, Emails) for professional demos. |
| **Audit Log** | JSON | Flexible storage for unstructured "Bad Data" errors. |

---

## Setup & Execution Instructions

**Prerequisites:** Python 3.x installed.

1.  **Clone the Repository**
    ```bash
    git clone [https://github.com/Srikumaran5557/Retail-pulse-ETL-Snickers](https://github.com/Srikumaran5557/Retail-pulse-ETL-Snickers)
    cd retail-pulse-etl
    ```

2.  **Install Dependencies**
    ```bash
    pip install pandas numpy faker
    ```

3.  **Run the Pipeline**
    The solution is a single-click execution. Run the master script:
    ```bash
    python solution_script.py
    ```

4.  **Verify Output**
    * **Console:** Watch the step-by-step logs for "✅ Success" messages.
    * **Files:** Check the root folder for:
        * `retail_hackathon.db` (The populated database)
        * `validation_anomalies.csv` (The log of injected errors)
        * `validation_ground_truth_customers.csv` (The correct answer key)

---

## 📂 Repository Structure

```text
📁 Report/
📁 RetailPulse/
├── 📄 solution_script.py      # The MASTER code (Generation + ETL + Analytics)
├── 📄 README.md               # This documentation
├── 📄 requirements.txt        # Dependencies list
├── 📄 ER Diagram.png          # Database Schema Image
├── 📄 Python_ETL.png          # Architecture Flow Image
├── 📁 output_csvs/            # Generated raw CSV files (created at runtime)
│   ├── stores.csv
│   ├── products.csv
│   ├── customer_details.csv
│   ├── promotion_details.csv
│   ├── loyalty_rules.csv
│   ├── store_sales_header.csv
│   ├── store_sales_line_items.csv
│   ├── validation_anomalies.csv
│   ├── validation_ground_truth_new_customer.csv
│   └── validation_ground_truth_customers.csv
└── 📁 database/
    └── retail_hackathon.db    # The final SQLite database (created at runtime)
