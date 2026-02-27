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

### 4.1 Updated architecture diagram

![University Threat Model](./diagrams/arch_updated.png)

### 4.2 Security Control Justifications

| Control ID | Category | Proposed Control | Justification (Risk Mitigation) |
| :--- | :--- | :--- | :--- |
| **C-01** | **IAM** | **Multi-Factor Auth (MFA)** | Mitigates **T-01**: Prevents account takeover even if passwords are leaked. |
| **C-02** | **Logic** | **Server-Side RBAC** | Mitigates **T-02**: Ensures users can only access their own data via strict Role-Based Access Control. |
| **C-03** | **Network** | **Web App Firewall (WAF)** | Mitigates **T-03**: Filters SQLi and XSS patterns before they reach the services. |
| **C-04** | **Data** | **AES-256 & TLS 1.3** | Mitigates **T-04**: Protects data-in-transit and at-rest via strong encryption. |
| **C-05** | **Logging** | **Immutable Audit Logs** | Mitigates **T-05**: Ensures a permanent record of all sensitive academic changes. |
| **C-06** | **Network** | **VPN / IP Whitelisting** | Mitigates **T-06**: Hides the Admin Portal from the public internet entirely. |

---
##  Task 5: Risk Treatment and Residual Risk

### 5.1 Risk Treatment Table

| Threat ID | Threat Name | Risk Level | Treatment Strategy | Mitigation Action |
| :--- | :--- | :--- | :--- | :--- |
| **T-01** | Auth Failures | **High** | **Mitigate** | Implementation of MFA and strict account lockout policies. |
| **T-02** | Access Control | **High** | **Mitigate** | Deployment of a centralized RBAC validation layer for all API calls. |
| **T-03** | Injection | **High** | **Mitigate** | Use of WAF filtering and enforced parameterized SQL queries. |
| **T-04** | Crypto Failures | **Medium** | **Mitigate** | Enforcing TLS 1.3 for transit and AES-256 for data at rest. |
| **T-05** | Logging Failure | **High** | **Mitigate** | Streaming logs to an immutable ELK stack to prevent tampering. |
| **T-06** | Admin Exposure | **High** | **Avoid** | Moving the Admin Portal behind a Zero-Trust VPN tunnel. |

---

### 5.2 Residual Risk Explanation
Despite the implementation of robust security controls, some residual risk remains. 

* **Social Engineering:** While MFA (**C-01**) is in place, users may still fall victim to sophisticated phishing or MFA-fatigue attacks, allowing unauthorized entry.
* **Zero-Day Vulnerabilities:** A previously unknown vulnerability in the WAF (**C-03**) or the underlying SQL database engine could be exploited before a patch is released.
* **Privileged Insider Threats:** An administrator with valid VPN access (**C-06**) could still abuse their legitimate permissions to access sensitive data, though this would be recorded by the immutable logs (**C-05**).
* **Logic Errors:** Complex RBAC rules (**C-02**) may contain bugs that allow accidental over-privileged access in specific edge cases that were not covered during testing.

### 6.1 System Overview
The University Management System is a distributed web application designed to handle sensitive academic, financial, and personal data. The system has been transformed from a vulnerable, public-facing architecture into a hardened, "Secure-by-Design" environment.

### 6.2 Assumptions and Limitations
* **Assumptions:** It is assumed that the Third-Party Integrations (Stripe, Azure AD) maintain their own security compliance (PCI-DSS/SOC2).
* **Limitations:** This report focuses on the web architecture; it does not cover physical security of the data centers or the security of individual end-user devices (laptops/phones).
* **Availability:** While security controls are high, this design assumes the University has the budget to maintain the WAF and VPN licensing costs.
