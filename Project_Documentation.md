# SENTINEL — AI-Powered Insider Threat Detection & Behavioural Monitoring

**BACHELOR OF TECHNOLOGY IN COMPUTER SCIENCE AND ENGINEERING**

**BY**
* S.MANJULA DEVI (24501A05M5)
* V.LALITH KUMAR REDDY (24501A05P4)
* S.BHARGAVA SAI (24501A05K2)
* SHEIK TASMIYA (24501A05L6)

**PRASAD V POTLURI SIDDHARTHA INSTITUTE OF TECHNOLOGY**
(Permanently affiliated to JNTU Kakinada, Approved by AICTE)
(An NBA & NAAC A+ accredited and ISO 21001:2018 Certified Institution)
Kanuru, Vijayawada – 520007
(2025-26)

---

## CERTIFICATE
This is to certify that the project report title “SENTINEL — AI-Powered Insider Threat Detection & Behavioural Monitoring” is the bonafied work of S.Manjula Devi (24501A05M5), V.Lalith Kumar Reddy (24501A05P4), S.Bhargava Sai (24501A05K2), Sheik Tasmiya (24501A05L6) in partial fulfilment of completing the Academic project in Data Warehousing & Data Mining during the academic year 2025-26.

---

## INDEX

1. Abstract
2. Introduction
3. Project Design
4. Dataset Overview
5. Data Preprocessing
6. Model Training and Hyperparameter Tuning
7. Model Evaluation and Selection
8. Model Saving
9. Web Application
10. Prediction Workflow
11. Threat Risk Scoring
12. Application Deployment
13. Technologies Used
14. Project Workflow from Start to End
15. Conclusion
16. Future Scope
17. References

---

## 1. Abstract
Modern banking organisations face significant risks from insider threats, where authorised personnel misuse their access. Detecting these threats manually is nearly impossible given the sheer volume of daily interactions. 

This project, **“SENTINEL — AI-Powered Insider Threat Detection & Behavioural Monitoring,”** develops an intelligent security pipeline that detects insider threats by analyzing the telemetry that applications naturally produce. The system evaluates employee actions against behavioural baselines to determine risk levels and flag anomalous behaviour.

The system uses a combination of **11 deterministic rules** and a machine learning model (**Isolation Forest**) to establish what is "normal" for an employee and to detect deviations. The input features include employee activities such as sensitive resource access, login locations, workstations, and transfer operations.

Data is collected continuously and fed into a risk engine that computes a decayed risk score from 0–100, categorized into 5 threat bands. The machine learning pipeline calculates a baseline deviation score, which is combined with rule violations to trigger actionable alerts for security analysts.

The completed system features a full-stack implementation with a **FastAPI backend** and a **React-based frontend**. It includes live demonstration scenarios (e.g., unusual location, mass lookup, sensitive download, and exfiltration) to practically validate the detection pipeline's effectiveness.

---

## 2. Introduction

### 2.1 Background
Banking institutions manage highly sensitive customer, financial, and operational data. While perimeter security focuses on external threats, insider threats—whether malicious or accidental—can bypass traditional defenses because the actors already possess valid credentials and access rights. 

Monitoring such behaviour requires analyzing the history of an employee's actions to define a baseline. By employing machine learning, specifically anomaly detection, organizations can continuously monitor telemetry data to flag actions that significantly deviate from historical norms.

### 2.2 Motivation
The main motivations of this project are:
*   **Proactive Threat Hunting:** Detecting reconnaissance and exfiltration attempts before significant damage occurs.
*   **Behavioural Baselines:** Moving beyond static rules to understand what is normal for a *specific* employee.
*   **End-to-End System Design:** Providing practical experience in full-stack development, database design, API construction, and ML integration.
*   **Auditable Security:** Ensuring an append-only architecture where activity and audit logs cannot be tampered with.

### 2.3 Problem Statement
To develop an AI-powered system that detects insider threats by combining deterministic security rules with an Isolation Forest machine learning model, evaluating employee telemetry to assign risk scores and generate alerts.

### 2.4 Objectives
The major objectives are:
1.  To simulate realistic banking telemetry and employee activity.
2.  To define and implement deterministic security rules for known threat vectors.
3.  To establish individual behavioural baselines.
4.  To implement an Isolation Forest model to detect anomalous activities.
5.  To build a risk engine that calculates dynamic threat scores.
6.  To enforce strict RBAC (Role-Based Access Control) and security constraints.
7.  To develop a robust FastAPI backend.
8.  To create a responsive React frontend for security analysts.
9.  To deploy the application and demonstrate specific threat scenarios.

---

## 3. Project Design

The overall workflow of the detection pipeline operates continuously:

**Employee Action → Security Telemetry (activities) → Behavioural Baseline Retrieval → Deterministic Rules Evaluation + Isolation Forest Prediction → Baseline Deviation Check → Risk Engine Scoring (0-100) → Threat Alert Generation (Anomalies) → Analyst Investigation (Audit Logs)**

The architecture is explicitly designed so that the detection pipeline is the core focus, with the synthetic banking data and UI serving to generate and visualize the telemetry.

---

## 4. Dataset Overview

Since real banking data cannot be used, the project relies on a highly realistic **synthetic dataset** generated specifically for this application. The API seeds a synthetic bank including branches, customers, accounts, transactions, loans, and documents upon startup.

### 4.1 Feature/Attribute Details
Telemetry data includes features such as:
*   `Action_Type` - Nature of the interaction (e.g., download, login, lookup)
*   `Resource_Sensitivity` - Classification of the accessed data (e.g., CONFIDENTIAL, RESTRICTED)
*   `Time_of_Day` - Timestamp of the action to detect after-hours activity
*   `Device/Workstation` - Information regarding the client device used
*   `Location` - Approximate city/country derived from the event
*   `Volume/Frequency` - Number of records accessed in a given timeframe
*   `Transfer_Destination` - e.g., USB transfer (simulated) or external endpoint

---

## 5. Data Preprocessing

To train the anomaly detection model, history of employee behavior must be preprocessed and feature-engineered.

### 5.1 Data Inspection & Generation
A fresh database starts with no behavioural history. The system generates 30 days of ordinary working activity (`history?days=30`) to recompute baselines and train the model.

### 5.2 Feature Engineering
Numerical and categorical data extracted from telemetry (like hour of day, access volume, device novelty) are transformed into feature vectors.

### 5.3 Baseline Computation
For each employee, standard operational parameters are calculated. What constitutes a "mass lookup" for a teller might be normal daily activity for an analyst.

### 5.4 ML Pipeline Integration
The preprocessed features are structured into `NumPy` arrays and `pandas` DataFrames before being passed to the `scikit-learn` models.

---

## 6. Model Training and Hyperparameter Tuning

### 6.1 Deterministic Rules Engine
Before ML is applied, the system evaluates actions against 11 hard-coded deterministic rules (e.g., `RULE-007` for after-hours access, `RULE-009` for USB transfers).

### 6.2 Isolation Forest Classifier
The primary machine learning component is the **Isolation Forest**. Isolation Forest is an unsupervised learning algorithm that detects anomalies by isolating observations. Random partitioning produces noticeably shorter paths for anomalies.

The model is trained on the synthetic 30-day behavioural history. When a new action occurs, it is scored by the Isolation Forest. If the deviation from the norm is substantial, it contributes heavily to the risk score.

### 6.3 Risk Score Calibration
Care is taken to ensure that the ML score does not completely dominate the deterministic rules. The risk engine fuses both outputs to compute a final risk value.

---

## 7. Model Evaluation and Selection

### 7.1 Scenario-Based Evaluation
Instead of standard static metrics (like Accuracy or F1-score on a test set), the model's effectiveness is evaluated by driving real events through the pipeline via predefined scenarios:
*   `normal`: Ordinary working day → No alert
*   `unusual_location`: Access from unseen city → RULE-001, RULE-010
*   `mass_lookup`: Sweep of the customer book → RULE-004, RULE-005
*   `sensitive_download`: Repeated CONFIDENTIAL downloads → RULE-006, RULE-008
*   `combined_insider`: Reconnaissance → Enumeration → Collection → Exfiltration → CRITICAL alert

### 7.2 Progressive Escalation
The system successfully returns progressive score escalations step-by-step for complex scenarios like `combined_insider`, proving that the risk engine accumulates and decays threats correctly over time.

---

## 8. Model Saving
Because behavioral baselines are dynamic and constantly updating, the ML models (or baseline parameters) are persistently stored and re-evaluated by the backend. The backend manages the state of the model directly via the database and runtime memory, ensuring the API is always scoring against the latest baselines.

---

## 9. Web Application Interface

A full-stack web application was developed for both employees and security personnel.
*   **Employee Portal:** Used by Tellers, Managers, etc., to perform daily banking tasks (which generates the telemetry).
*   **Security Console:** A React-based interface used by `ADMIN` and `SECURITY_ANALYST` roles. It provides real-time visualization of risk scores, active alerts, and audit logs. Separation of duties ensures employees cannot access the console, and analysts cannot access the banking portal.

---

## 10. Prediction Workflow
When an employee performs an action in the frontend:
**API Request → Telemetry Recorded → Pipeline Triggered → Rules Engine & Isolation Forest Score the Event → Risk Score Updated → Database Appended → Alert Pushed to Security Console (if threshold exceeded)**

---

## 11. Threat Risk Scoring
The application evaluates risk on a scale of 0 to 100, broken into 5 severity bands (e.g., LOW, MODERATE, HIGH, CRITICAL). The scoring mechanism includes temporal decay, meaning an isolated minor anomaly will naturally reduce in risk over time if no further suspicious activity occurs.

---

## 12. Application Deployment

The project is designed for cloud deployment, utilizing separated frontend and backend services.
*   **Frontend (Security Console):** Deployed via modern cloud hosting (e.g., Railway/Vercel) using `VITE_API_URL` to connect to the backend.
*   **Backend API:** Deployed on cloud platforms running Uvicorn and FastAPI, connected to a PostgreSQL database.

---

## 13. Technologies Used

| Layer | Choice |
| --- | --- |
| **API** | FastAPI, SQLAlchemy 2.x, Alembic, Pydantic v2 |
| **Database** | PostgreSQL (application), SQLite (tests) |
| **Auth** | JWT (HS256) via python-jose, bcrypt via passlib |
| **Machine Learning** | scikit-learn `IsolationForest`, NumPy, pandas |
| **UI** | React 19 + TypeScript, Vite, Tailwind CSS v4, Recharts 3 |
| **Testing** | Pytest |

---

## 14. Project Workflow from Start to End

1. **System Architecture Design:** Mapping the detection pipeline.
2. **Backend Setup:** Initializing FastAPI, Alembic migrations, and SQLAlchemy models.
3. **Synthetic Data Generation:** Developing scripts to seed banking data and telemetry.
4. **Security Rules Implementation:** Coding the 11 deterministic threat rules.
5. **Machine Learning Integration:** Implementing the Isolation Forest and behavioral baselines.
6. **Risk Engine Construction:** Building the 0-100 scoring system with time decay.
7. **API Endpoints Creation:** Exposing routes for operations, telemetry, and security oversight.
8. **Frontend Development:** Building the React dashboard with Tailwind CSS.
9. **Scenario Testing:** Validating threat vectors via `pytest` and simulated endpoint calls.
10. **Deployment:** Hosting the application on live servers (e.g., Railway).

---

## 15. Conclusion
The **SENTINEL** project successfully demonstrates a highly realistic approach to insider threat detection. By avoiding simplistic CRUD operations and focusing entirely on a robust telemetry pipeline, it proves that organizations can leverage their existing application data to find threats. The combination of hard-coded rules and Isolation Forest anomaly detection provides a balanced, accurate, and auditable security solution.

## 16. Future Scope
*   **Advanced Real-Time Streaming:** Moving the telemetry ingestion to a message broker like Apache Kafka for massive scale.
*   **Complex Graph Analytics:** Analyzing relationships between compromised accounts using graph databases (e.g., Neo4j).
*   **Automated Response Procedures:** Automatically locking accounts or blocking IPs when risk scores hit CRITICAL.
*   **Enhanced Frontend Dashboards:** Integrating more granular data visualization and timeline reconstruction tools for analysts.

## 17. References
1. Project GitHub Repository & Internal Documentation (`docs/`)
2. FastAPI & SQLAlchemy Documentation
3. Scikit-learn Documentation (Isolation Forest)
4. React & Tailwind CSS Documentation
5. Insider Threat Detection Best Practices (General Cybersecurity Principles)
