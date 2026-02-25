# CyberSecurity_Architecture

graph TD
    %% User Groups
    subgraph Users [External / Public Zone]
        Student["Student / Faculty / Alumni (Web Browser)"]
        Recruiter["Recruiter (External Job Portal)"]
    end

    %% Trust Boundary 1
    subgraph DMZ [Public Facing Zone]
        direction TB
        FW1["Firewall / WAF"]
        FE["Web Frontend (React SPA)"]
        GW["API Gateway"]
    end

    %% Trust Boundary 2
    subgraph PrivateApp [University Private Cloud / Trusted Zone]
        direction TB
        Auth["Identity & Access Service"]
        Acad["Academic Service (LMS, Attendance)"]
        Fin["Financial Service (Fees & Salary)"]
        Log["Logging & ELK Stack"]
    end

    %% Data Layer
    subgraph DataLayer [Data Storage Zone]
        DB[("Relational DB (SQL)")]
        Obj["Object Storage (Docs/Videos)"]
        Cache["Redis Cache"]
    end

    %% Administrative Path
    subgraph AdminZone [Management Plane]
        Admin["Registrar / Admin Portal"]
    end

    %% External Integrations
    subgraph External [Third-Party Integrations]
        IdP["Identity Provider (Azure AD/SSO)"]
        Pay["Payment Gateway (Stripe)"]
        Lib["External Library Services (JSTOR/IEEE)"]
    end

    %% Connections
    Users --> FW1
    FW1 --> FE
    FE --> GW
    GW --> Auth
    GW --> Acad
    GW --> Fin
    
    Auth -.-> IdP
    Fin -.-> Pay
    Acad -.-> Lib
    
    %% Internal Logic
    Acad & Fin & Auth --> DB
    Acad --> Obj
    Auth --> Cache
    
    %% Admin Access (The restricted path)
    Admin ==>|VPN / Secure Tunnel| Auth
    Admin ==>|Direct Access| Log

    %% Styling Trust Boundaries
    style DMZ fill:#f9f,stroke:#333,stroke-dasharray: 5 5
    style PrivateApp fill:#ccf,stroke:#333
    style DataLayer fill:#cfc,stroke:#333
    style AdminZone fill:#fcc,stroke:#333
