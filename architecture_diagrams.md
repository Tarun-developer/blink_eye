# Blink Eye Hospitals: System Architecture

## Overview

The platform uses a cloud-based setup with separate websites for each hospital.

```mermaid
graph TB
    P[Patients] --> W[Web Interfaces]
    S[Staff] --> W
    W --> A[API Gateway]
    A --> M[Microservices]
    M --> D[(MySQL Database)]
    M --> F[(File Storage)]
    M --> C[(Redis Cache)]
```

## Key Diagrams

### Database Structure
```mermaid
erDiagram
     tenants ||--o{ users : "has"
     users ||--o{ patients : "manages"
     patients ||--o{ appointments : "books"
     users ||--o{ appointments : "provides"
```

### User Roles
- Super Admin: Full access
- Hospital Admin: Hospital management
- Doctor: Patient care
- Patient: Self-service