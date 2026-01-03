# Blink Eye Hospitals Platform: Architecture Diagrams

This document provides a comprehensive set of architecture diagrams for the Blink Eye Hospitals platform, consolidating and enhancing designs from previous analyses. All diagrams are created using Mermaid format for clarity and consistency.

## 1. System Overview

```mermaid
graph TB
    subgraph "User Interfaces"
        PP[Patient Portal<br/>Web Interface]
        MA[Mobile Apps<br/>iOS/Android]
        WA[Web Admin<br/>Dashboard]
    end

    subgraph "API Layer"
        AG[API Gateway<br/>Authentication & Routing]
        MS[Microservices<br/>EHR, Appointments, Billing, etc.]
    end

    subgraph "Data Layer"
        DB[(PostgreSQL Database<br/>Multi-tenant with RLS)]
        FS[(File Storage<br/>Documents, Images)]
        CACHE[(Redis Cache<br/>Session & Data)]
    end

    subgraph "External Systems"
        ES[External Services<br/>Payment, Email, SMS]
        INT[Third-party Integrations<br/>Insurance, Devices]
    end

    PP --> AG
    MA --> AG
    WA --> AG
    AG --> MS
    MS --> DB
    MS --> FS
    MS --> CACHE
    MS --> ES
    MS --> INT

    classDef userInterface fill:#e1f5fe
    classDef apiLayer fill:#f3e5f5
    classDef dataLayer fill:#e8f5e8
    classDef external fill:#fff3e0

    class PP,MA,WA userInterface
    class AG,MS apiLayer
    class DB,FS,CACHE dataLayer
    class ES,INT external
```

### Explanation
This system overview diagram illustrates the high-level architecture of the Blink Eye Hospitals platform. The platform follows a multi-tenant, cloud-based architecture with subdomain isolation. Key components include:

- **User Interfaces**: Patient portal for self-service, mobile apps for on-the-go access, and web admin dashboards for hospital staff
- **API Layer**: API Gateway handles authentication, routing, and load balancing; microservices provide modular functionality for different business domains
- **Data Layer**: PostgreSQL with Row-Level Security (RLS) for tenant isolation, file storage for documents/images, and Redis for caching
- **External Systems**: Integrations with payment processors, communication services, and third-party healthcare systems

The architecture supports horizontal scaling, with each tenant operating in isolation while sharing the underlying infrastructure.

## 2. Database ERD

```mermaid
erDiagram
    tenants ||--o{ users : "has"
    tenants ||--o{ roles : "has"
    tenants ||--o{ patients : "has"
    tenants ||--o{ appointments : "has"
    tenants ||--o{ medical_records : "has"
    tenants ||--o{ doctor_documents : "has"
    tenants ||--o{ accommodations : "has"
    tenants ||--o{ leads : "has"
    tenants ||--o{ content_tenant_variants : "has"
    tenants ||--o{ billing_invoices : "has"
    tenants ||--o{ inventory_items : "has"
    tenants ||--o{ telemedicine_sessions : "has"
    tenants ||--o{ user_roles : "has"
    tenants ||--o{ role_permissions : "has"

    users ||--o{ user_roles : "assigned"
    roles ||--o{ user_roles : "assigned"
    roles ||--o{ role_permissions : "has"
    permissions ||--o{ role_permissions : "granted"

    patients ||--o{ appointments : "books"
    users ||--o{ appointments : "provides"
    patients ||--o{ medical_records : "has"
    users ||--o{ medical_records : "creates"
    users ||--o{ doctor_documents : "owns"
    patients ||--o{ accommodations : "occupies"
    patients ||--o{ billing_invoices : "receives"
    appointments ||--o{ billing_invoices : "generates"
    patients ||--o{ telemedicine_sessions : "participates"
    users ||--o{ telemedicine_sessions : "conducts"

    content_master ||--o{ content_tenant_variants : "customized by"
```

### Explanation
The Entity-Relationship Diagram (ERD) shows the multi-tenant database schema designed for the Blink Eye platform. Key features include:

- **Tenant Isolation**: All tenant-specific tables include a `tenant_id` foreign key to the `tenants` table
- **Row-Level Security**: PostgreSQL RLS policies ensure users can only access data for their assigned tenant
- **RBAC Integration**: Users, roles, and permissions are managed per tenant, with global permissions assigned to roles
- **Core Entities**: Patients, appointments, medical records, and billing form the heart of the EHR system
- **Operational Support**: Tables for inventory, telemedicine, accommodations, and content management support comprehensive hospital operations
- **Scalability**: Designed for partitioning by `tenant_id` to support horizontal scaling across multiple database instances

## 3. Tenant Model

```mermaid
graph TD
    subgraph "Global Platform"
        GP[Blink Eye Platform<br/>blinkeye.com]
        GT[(Global Tables<br/>permissions, content_master)]
    end

    subgraph "Tenant 1: stjohns.blinkeye.com"
        T1[stjohns.blinkeye.com]
        T1DB[(Tenant Database<br/>tenant_id=1)]
        T1U[Users & Roles]
        T1D[Data & Content]
        T1C[Custom Branding]
    end

    subgraph "Tenant 2: mercy.blinkeye.com"
        T2[mercy.blinkeye.com]
        T2DB[(Tenant Database<br/>tenant_id=2)]
        T2U[Users & Roles]
        T2D[Data & Content]
        T2C[Custom Branding]
    end

    GP --> T1
    GP --> T2
    GT --> T1DB
    GT --> T2DB

    T1 --> T1DB
    T1DB --> T1U
    T1DB --> T1D
    T1DB --> T1C

    T2 --> T2DB
    T2DB --> T2U
    T2DB --> T2D
    T2DB --> T2C

    classDef global fill:#ffebee
    classDef tenant fill:#e8f5e8
    classDef data fill:#e3f2fd

    class GP,GT global
    class T1,T2 tenant
    class T1DB,T2DB,T1U,T1D,T1C,T2U,T2D,T2C data
```

### Explanation
The tenant model diagram illustrates the multi-tenant architecture with subdomain-based isolation. Key aspects include:

- **Subdomain Isolation**: Each hospital operates on a unique subdomain (e.g., stjohns.blinkeye.com) ensuring complete separation
- **Data Partitioning**: All tenant data is tagged with `tenant_id` and protected by Row-Level Security policies
- **Shared Infrastructure**: Tenants share the same application code and database schema while maintaining data isolation
- **Customization**: Each tenant can have custom branding, content variants, and workflow configurations
- **Global vs. Tenant Scope**: Global tables (permissions, content_master) are shared across tenants, while tenant-specific tables are isolated
- **Scalability**: Architecture supports adding new tenants without code changes, with potential for database sharding by tenant_id

## 4. Data Flow

```mermaid
sequenceDiagram
    participant P as Patient
    participant PP as Patient Portal
    participant MA as Mobile App
    participant AG as API Gateway
    participant MS as Microservices
    participant DB as Database
    participant FS as File Storage
    participant EXT as External Services

    P->>PP: Browse services & book appointment
    PP->>AG: API request with auth token
    AG->>MS: Route to Appointment Service
    MS->>DB: Query availability (tenant_id filter)
    DB-->>MS: Return available slots
    MS-->>AG: Formatted response
    AG-->>PP: JSON response
    PP-->>P: Display booking form

    P->>PP: Submit appointment booking
    PP->>AG: POST appointment data
    AG->>MS: Create appointment
    MS->>DB: Insert appointment (with tenant_id)
    DB-->>MS: Confirmation
    MS->>EXT: Send confirmation email/SMS
    EXT-->>MS: Success
    MS-->>AG: Success response
    AG-->>PP: Booking confirmed

    Note over MA: Staff using mobile app
    MA->>AG: Login request
    AG->>MS: Authenticate user
    MS->>DB: Verify credentials & tenant access
    DB-->>MS: User roles & permissions
    MS-->>AG: JWT token with tenant context
    AG-->>MA: Authenticated session

    MA->>AG: Access patient records
    AG->>MS: EHR service request
    MS->>DB: Query medical_records (RLS enforced)
    DB-->>MS: Filtered patient data
    MS->>FS: Retrieve attachments if needed
    FS-->>MS: File data
    MS-->>AG: Complete patient record
    AG-->>MA: Display in app
```

### Explanation
The data flow diagram shows how information moves through the Blink Eye platform, emphasizing tenant isolation and security. Key flows include:

- **Patient Interactions**: Patients access the system through web portals or mobile apps, with all requests routed through the API Gateway
- **Authentication & Authorization**: Every request includes tenant context and user authentication, with RBAC permissions checked at the service layer
- **Data Isolation**: All database queries include `tenant_id` filters, enforced by Row-Level Security policies
- **Service Orchestration**: Microservices handle business logic, coordinating between database, file storage, and external services
- **External Integrations**: Communication with payment processors, email/SMS services, and third-party healthcare systems
- **Audit Trail**: All data access and modifications are logged for compliance and security monitoring

## 5. RBAC Hierarchy

```mermaid
graph TD
    SA[Super Admin<br/>Global Scope<br/>Full Platform Access] --> MD[Medical Director<br/>Tenant Scope<br/>Clinical Leadership]
    MD --> CA[City Admin<br/>Tenant Scope<br/>Operations Management]
    CA --> D[Doctor<br/>Tenant Scope<br/>Clinical Care]
    CA --> CE[Content Editor<br/>Tenant Scope<br/>Content Management]
    CA --> CR[CRM Agent<br/>Tenant Scope<br/>Lead Management]
    CA --> F[Finance<br/>Tenant Scope<br/>Billing & Payments]

    SA -->|Can hold| MD
    CA -->|Can combine with| F
    CA -->|Can combine with| CR
    MD -->|Can combine with| D

    classDef global fill:#ffebee
    classDef tenant fill:#e8f5e8
    classDef role fill:#e3f2fd

    class SA global
    class MD,CA,D,CE,CR,F tenant
```

### Explanation
The RBAC hierarchy diagram shows the role-based access control structure with inheritance and multi-role capabilities. Key features include:

- **Hierarchical Permissions**: Higher-level roles inherit all permissions from lower-level roles in the hierarchy
- **Scope Levels**: Super Admin has global access across all tenants; all other roles are tenant-specific
- **Multi-Role Support**: Users can hold multiple roles simultaneously, with permissions combined (union-based)
- **Role Definitions**:
  - **Super Admin**: Platform-wide management, tenant onboarding, system administration
  - **Medical Director**: Clinical oversight, staff management, quality assurance
  - **City Admin**: Operational management, resource allocation, policy implementation
  - **Doctor**: Patient care, medical records, appointment management
  - **Content Editor**: Website content, email templates, patient communications
  - **CRM Agent**: Lead management, patient outreach, conversion tracking
  - **Finance**: Billing, invoicing, payment processing, financial reporting
- **Permission Inheritance**: Ensures efficient permission management while maintaining security
- **Tenant Isolation**: All tenant-specific roles are restricted to their assigned tenant's data

These diagrams provide a comprehensive architectural overview of the Blink Eye Hospitals platform, supporting its goals of scalability, security, and multi-tenant operation.