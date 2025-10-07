# Azure-Hospital-Patient-Flow-Analytics
## 1. Business Background
Midwest Health Alliance (MHA) is a network of 7 hospitals across the Midwest.  
The organization faces challenges in **patient flow management**, especially during high-demand periods (e.g., seasonal flu outbreaks).

Currently, there is **no centralized real-time system** to monitor:  
- Bed occupancy  
- Patient admission/discharge patterns  
- Departmental load  

This lack of visibility leads to **longer patient waiting times** and **difficulty in optimizing hospital resources**.

---

## 2. Business Objectives
- Monitor patient admissions to **minimize waiting times**  
- Identify **department-level bottlenecks** (Emergency, Surgery, ICU, etc.)  
- Track **demographics** via gender and age KPIs  
- Automate alerts for **pipeline failures or data issues**  

---

## 3. Functional Requirements

### Data Sources
- Real-time patient admission/discharge events from hospital registration systems  
- Daily batch extracts from Electronic Health Records (EHR)  
- Department metadata (capacity, staff numbers)  

### Data Processing & Storage
- Medallion architecture: **Bronze → Silver → Gold**  
- Handle **schema evolution** for new patient attributes  
- Implement **SCD Type 2** for patient and department history  
- Create a **Star Schema** for analytics  

### Analytics
- Use **Azure Synapse** for query execution  

### Orchestration & Automation
- Use **Azure Data Factory (ADF)** to:  
  - Automate daily batch ingestion from EHR  
  - Trigger real-time streaming pipelines  
  - Refresh Gold-layer for analytics queries  

### Data Quality
- Simulate **dirty data** (missing admission times, invalid ages, duplicates, wrong timestamps)  
- Apply cleaning rules in Silver layer to standardize data  

### Security & Compliance
- Implement **Role-Based Access Control (RBAC)** across hospital departments  

---

## 4. Deliverables
- Fully functional **Azure-based data pipeline**  
- **Data quality and validation reports** to ensure analytics reliability  
- Documentation including **architecture diagrams, data models, and star schema**  

---

## 5. Success Criteria
- Pipelines run in **real-time and batch mode** without manual intervention  
- Full automation using **ADF**  
- Schema changes handled gracefully without downtime  

---

## 6. Architecture & Pipeline Flow

### Event Producer (Python Script)
- Simulates patient admission/discharge events  
- Publishes events to **Azure Event Hub**  
- Injects **dirty data** for testing cleaning logic  

### Bronze Layer (Databricks - Raw Data)
- Reads events from Event Hub (Kafka connector)  
- Stores raw JSON in **ADLS Gen2** in Delta format  

### Silver Layer (Databricks - Clean Data)
- Applies **schema enforcement and cleaning rules**  
- Converts timestamps and corrects invalid ages/future admission times  
- Writes standardized data to **ADLS Delta format**  

### Gold Layer (Databricks - Star Schema)
- Builds **Dimension and Fact tables**:  
  - **Patient Dimension** (SCD Type 2) – maintains patient history  
  - **Department Dimension** – unique departments with hospital mapping  
  - **Fact Table** – metrics like length of stay, bed occupancy, current admission status  

---

## 7. Data Model - Star Schema

### Dimensions
- **Patient Dimension:** Patient demographics with historical tracking  
- **Department Dimension:** Hospital department metadata  

### Fact Table
- Hospital admissions, **length of stay**, bed occupancy, admission timestamps  

---

## 8. Deployment / Next Steps

### Run Event Producer
```bash
python scripts/patient_event_producer.py
