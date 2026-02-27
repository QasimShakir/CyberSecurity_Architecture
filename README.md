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
![University Threat Model](./diagrams/arch.png)
---

### 2.1 Asset Inventory Table

| Asset ID | Asset Name | Description | Data Type | Location |
| :--- | :--- | :--- | :--- | :--- |
| **A-01** | **User Credentials** | Salted/Hashed passwords and MFA tokens. | Secrets | Identity DB |
| **A-02** | **Student PII** | Names, CNICs, and medical notes. | Personal Data | Relational DB |
| **A-03** | **Grade Records** | Final grades and transcripts. | Academic | Relational DB |
| **A-04** | **Financial Ledgers** | Fee payment and faculty payroll. | Financial | Financial DB |

###  3.1: Structured Threat Modeling (OWASP)

| Threat ID | Threat Area | OWASP Category | Description | Affected Component | Risk Level |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **T-01** | **Authentication** | **Auth Failures (A07)** | Brute force or credential stuffing targeting Faculty accounts. | Identity Service | **High** |
| **T-02** | **Authorization** | **Broken Access Control (A01)** | Student uses IDOR to view/edit another student's grade records. | Academic Service | **High** |
| **T-03** | **Data Storage** | **Injection (A03)** | SQL Injection used to dump the entire Fee/Payroll database. | Relational DB | **High** |
| **T-04** | **API Comm.** | **Cryptographic Failures (A02)** | Sensitive PII transmitted over unencrypted or weak TLS channels. | API Gateway | **Medium** |
| **T-05** | **Logging** | **Monitoring Failures (A09)** | Unauthorized grade changes occur without being captured in logs. | Logging System | **High** |
| **T-06** | **Admin Access** | **Security Misconfig (A05)** | Admin portal exposed to public internet without VPN/IP white-listing. | Admin Portal | **High** |

---

###  3.2: Risk Reasoning for Each Threat

* **T-01 (Authentication):** Ranked **High** because faculty accounts are high-value targets. Compromise leads to leaked exams and mass grade manipulation.
* **T-02 (Authorization):** Ranked **High** because students and faculty share the same API. If access control is broken, the "Trust Boundary" between roles disappears.
* **T-03 (Injection):** Ranked **High** because loss of data integrity in the database is often irreversible and destroys academic credibility.
* **T-04 (API Communication):** Ranked **Medium** because while data can be intercepted, it requires an active "Man-in-the-Middle" attacker on the network, making it slightly less likely than direct web attacks.
* **T-05 (Logging):** Ranked **High** because accountability is required for financial compliance. Without logs, insider threats (disgruntled staff) can commit fraud without detection.
* **T-06 (Admin Access):** Ranked **High** because the Admin Portal has "god-mode" permissions. Public exposure is a critical architecture flaw.

### 3.3: Threat Diagram
![University Threat Model](./diagrams/threat.png)
---

###  4.1: Secure Architecture Design (Controls)

| Control ID | Category | Proposed Architectural Control | Justification |
| :--- | :--- | :--- | :--- |
| **C-01** | **IAM** | **Multi-Factor Authentication (MFA)** | Blocks **T-01** by requiring more than just a password. |
| **C-02** | **Network** | **Web Application Firewall (WAF)** | Blocks **T-03** by filtering malicious SQL injection patterns. |
| **C-03** | **Network** | **Private Subnets (VPC)** | Protects the DB by ensuring it has no public IP address. |
| **C-04** | **Data Protection** | **TLS 1.3 & AES-256 Encryption** | Mitigates **T-04** for data in transit and at rest. |
| **C-05** | **Management** | **VPN / IP Whitelisting** | Solves **T-06** by hiding the Admin portal from the public web. |

### 4.2 Updated architecture diagram

![University Threat Model](./diagrams/arch-updated.png)
