# CyberSecurity_Architecture
### 2.1 Asset Inventory Table

| Asset ID | Asset Name | Description | Data Type | Location |
| :--- | :--- | :--- | :--- | :--- |
| **A-01** | **User Credentials** | Salted/Hashed passwords and MFA tokens. | Secrets | Identity DB |
| **A-02** | **Student PII** | Names, CNICs, and medical notes. | Personal Data | Relational DB |
| **A-03** | **Grade Records** | Final grades and transcripts. | Academic | Relational DB |
| **A-04** | **Financial Ledgers** | Fee payment and faculty payroll. | Financial | Financial DB |
