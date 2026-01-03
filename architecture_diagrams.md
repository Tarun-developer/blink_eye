# Blink Eye Hospitals: System Architecture

## Overview

The platform uses a cloud-based setup with separate websites for each hospital. The backend is a monolithic Laravel application that handles all business logic, with frontend rendered using Blade templates styled with Tailwind CSS for responsive and modern UI.

```mermaid
graph TB
    P[Patients] --> L[Laravel Application]
    S[Staff] --> L
    L --> D[(MySQL Database)]
    L --> F[(File Storage)]
    L --> C[(Redis Cache)]
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