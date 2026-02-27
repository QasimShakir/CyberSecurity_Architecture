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
```mermaid
graph TD
    subgraph PublicZone [Public Internet]
        Users["University Users (Student/Faculty/Alumni)"]
    end

    subgraph DMZ [DMZ / Entrance]
        GW["API Gateway / WAF"]
    end

    subgraph TrustedZone [University Internal Network]
        Auth["Identity Service (SSO)"]
        Acad["Academic Service (LMS/Grades)"]
        Fin["Financial Service (Fees/Salary)"]
        
        subgraph DataLayer [Data Storage]
            DB[(Central Database)]
            Files[(File Storage - LMS)]
        end
    end

    subgraph AdminZone [Management Plane]
        Admin["Registrar / Admin Portal"]
    end

    Users -->|HTTPS| GW
    GW --> Auth
    GW --> Acad
    GW --> Fin
    Acad & Fin & Auth --> DB
    Acad --> Files
    Admin ==>|Secure Tunnel| Auth
    Admin ==>|Audit Logs| DB
### 2.1 Asset Inventory Table

| Asset ID | Asset Name | Description | Data Type | Location |
| :--- | :--- | :--- | :--- | :--- |
| **A-01** | **User Credentials** | Salted/Hashed passwords and MFA tokens. | Secrets | Identity DB |
| **A-02** | **Student PII** | Names, CNICs, and medical notes. | Personal Data | Relational DB |
| **A-03** | **Grade Records** | Final grades and transcripts. | Academic | Relational DB |
| **A-04** | **Financial Ledgers** | Fee payment and faculty payroll. | Financial | Financial DB |
