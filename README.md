# CyberSecurity_Architecture

# Secure Architecture & Design: University Management System (UMS)


##  Task 1: System Definition and Architecture

### 1.1 Application Components
The University Management System is a service-oriented application designed to handle academic, financial, and administrative functions.

* **Web Portal (Frontend):** A React-based SPA providing interfaces for Students, Faculty, and Admins.
* **API Gateway:** The central entry point for all traffic, managing routing and SSL termination.
* **Identity Service:** Manages AuthN/AuthZ, RBAC, and integrates with External IdPs (Azure AD).
* **Academic Service:** Handles LMS features, grade entries, and attendance tracking.
* **Financial Service:** Manages student fee processing and faculty payroll.
* **Data Layer:** Consists of a Central SQL Database for structured records and Object Storage for course files.

### 1.2 Architecture Diagram

### 2.1 Asset Inventory Table

| Asset ID | Asset Name | Description | Data Type | Location |
| :--- | :--- | :--- | :--- | :--- |
| **A-01** | **User Credentials** | Salted/Hashed passwords and MFA tokens. | Secrets | Identity DB |
| **A-02** | **Student PII** | Names, CNICs, and medical notes. | Personal Data | Relational DB |
| **A-03** | **Grade Records** | Final grades and transcripts. | Academic | Relational DB |
| **A-04** | **Financial Ledgers** | Fee payment and faculty payroll. | Financial | Financial DB |
