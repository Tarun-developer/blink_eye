# Blink Eye Hospitals Platform: User Flows and Page Structures Outline

## Overview
This document outlines the key user flows and page structures for the Blink Eye Hospitals platform, organized by subdomain (tenant). The platform uses a multi-tenant architecture with subdomain-based isolation (e.g., hospital.blinkeye.com). Each tenant can customize branding, content, and workflows while maintaining core functionality.

## Patient-Facing Pages and User Flows

### Homepage
**URL Structure**: https://{subdomain}.blinkeye.com/
**Purpose**: First impression and navigation hub for patients and visitors.

**Key Components**:
- Hero banner with tenant-specific branding and emergency contact
- Quick appointment booking widget
- Services overview carousel
- Doctor spotlight section
- Patient testimonials (tenant-customizable)
- News/announcements feed
- Contact information and location map
- Multi-language selector

**User Flows**:
1. Visitor lands on homepage
2. Browses services or doctors
3. Books appointment via quick widget
4. Registers for patient portal
5. Accesses emergency information

**Tenant Adaptations**:
- Custom logo, colors, and images
- Localized content and language support
- Hospital-specific services and specialties
- Regional emergency contacts

### Services Page
**URL Structure**: https://{subdomain}.blinkeye.com/services
**Purpose**: Showcase medical services and treatments offered.

**Key Components**:
- Service categories (Cataract Surgery, Glaucoma, Retina Care, etc.)
- Detailed service descriptions with images
- Pricing information (where applicable)
- Doctor specializations per service
- FAQ section
- Call-to-action for appointments

**User Flows**:
1. Patient browses service categories
2. Selects specific service for details
3. Views associated doctors
4. Books appointment for selected service
5. Downloads informational materials

**Tenant Adaptations**:
- Service offerings based on hospital capabilities
- Local pricing and insurance information
- Regional health regulations compliance
- Custom service descriptions and media

### Doctors Page
**URL Structure**: https://{subdomain}.blinkeye.com/doctors
**Purpose**: Display medical staff profiles and specializations.

**Key Components**:
- Doctor directory with search/filter
- Individual doctor profiles (photo, bio, specialties, education)
- Availability calendar
- Patient reviews and ratings
- Appointment booking integration

**User Flows**:
1. Patient searches for doctors by specialty or name
2. Views doctor profiles and reviews
3. Checks availability
4. Books appointment with selected doctor
5. Leaves review after visit

**Tenant Adaptations**:
- Staff-specific profiles and photos
- Local certifications and languages spoken
- Hospital-specific achievements
- Custom review systems

### Appointments Page
**URL Structure**: https://{subdomain}.blinkeye.com/appointments
**Purpose**: Self-service appointment booking and management.

**Key Components**:
- Appointment booking form
- Calendar view with availability
- Service/doctor selection
- Patient information form
- Confirmation and reminder system

**User Flows**:
1. Patient selects service and doctor
2. Chooses available time slot
3. Enters personal and insurance information
4. Receives confirmation and reminders
5. Manages/cancels appointments via patient portal

**Tenant Adaptations**:
- Custom appointment types and durations
- Local insurance provider integration
- Regional holiday and operating hour considerations
- Multi-language booking forms

### Accommodations Page
**URL Structure**: https://{subdomain}.blinkeye.com/accommodations
**Purpose**: Information about patient accommodations and stays.

**Key Components**:
- Room types and amenities
- Pricing and availability
- Booking integration with appointments
- Facility photos and virtual tours
- Policies and procedures

**User Flows**:
1. Patient views accommodation options
2. Books room in conjunction with appointment
3. Manages stay details
4. Accesses facility information
5. Provides feedback post-stay

**Tenant Adaptations**:
- Hospital-specific room configurations
- Local amenities and services
- Regional pricing and regulations
- Custom policies for international patients

## Admin Dashboards

### Global Admin Dashboard
**URL Structure**: https://admin.blinkeye.com/
**Purpose**: Platform-wide oversight and management.

**Key Components**:
- Tenant overview and health metrics
- System-wide analytics and KPIs
- User management across tenants
- Global settings and configurations
- Audit logs and compliance reports

**User Flows** (Super Admin):
1. Logs in with global credentials
2. Monitors platform health and performance
3. Manages tenant onboarding and settings
4. Reviews system-wide reports
5. Handles escalations and support

**Tenant Adaptations**: N/A (global scope)

### City-Specific Admin Dashboard
**URL Structure**: https://{subdomain}.blinkeye.com/admin
**Purpose**: Hospital-specific operational management.

**Key Components**:
- Staff management and scheduling
- Patient overview and appointment management
- Financial dashboards and billing
- Inventory and resource tracking
- Local analytics and reporting

**User Flows** (City Admin, Medical Director):
1. Logs in to tenant-specific dashboard
2. Manages staff roles and permissions
3. Oversees daily operations and appointments
4. Reviews financial and operational metrics
5. Handles local policy and content management

**Tenant Adaptations**:
- Custom dashboards based on hospital size and services
- Local regulatory compliance tools
- Regional language and currency support
- Hospital-specific KPIs and workflows

## Internal Workflows

### Doctor Onboarding Workflow
1. New doctor registers via admin invitation
2. Uploads credentials and documents
3. City Admin reviews documentation
4. Medical Director approves verification
5. System grants clinical permissions
6. Doctor profile goes live on patient site

### Patient Registration Workflow
1. Patient books appointment or visits hospital
2. Staff creates patient record
3. Collects personal and medical information
4. Assigns medical record number
5. Sets up patient portal access
6. Links to existing records if applicable

### Appointment Management Workflow
1. Patient or staff creates appointment
2. System checks doctor availability
3. Sends confirmation to patient
4. Doctor reviews pre-appointment notes
5. Conducts consultation and updates records
6. Generates billing and follow-up reminders

## User Journey Maps

### Patient Booking Appointment
```
Start: Patient needs eye care
→ Discovers hospital via search/referral
→ Visits homepage, browses services
→ Selects service and doctor
→ Books appointment online
→ Receives confirmation and reminders
→ Arrives for appointment
→ Completes consultation
→ Receives follow-up care instructions
→ Pays bill and leaves review
End: Patient satisfied with care
```

### Doctor Managing Profile
```
Start: Doctor joins hospital
→ Receives admin invitation
→ Creates account and uploads credentials
→ City Admin verifies documents
→ Medical Director approves
→ Doctor gains access to patient records
→ Updates profile information
→ Manages appointment schedule
→ Conducts consultations
→ Updates patient records
→ Receives performance feedback
End: Doctor actively contributing to patient care
```

### Admin Overseeing Operations
```
Start: Admin begins shift
→ Logs into dashboard
→ Reviews daily metrics and alerts
→ Manages staff scheduling
→ Oversees appointment bookings
→ Monitors financial performance
→ Handles patient inquiries
→ Updates content and policies
→ Generates reports
→ Plans for upcoming week
End: Operations running smoothly
```

## Page Layouts and Key Components

### Common Layout Structure
- **Header**: Logo, navigation menu, user account, language selector
- **Main Content**: Page-specific content with responsive grid
- **Sidebar**: Quick actions, recent activity, notifications
- **Footer**: Contact info, links, compliance notices

### Key Reusable Components
- **Appointment Widget**: Service/doctor/time selection
- **Patient Card**: Photo, name, MRN, last visit
- **Doctor Profile Card**: Photo, specialty, rating, availability
- **Dashboard Metrics Cards**: KPIs with charts
- **Data Tables**: Sortable, filterable lists
- **Forms**: Validated input forms with progress indicators
- **Modals**: Confirmations, details, quick edits

### Responsive Design
- Mobile-first approach
- Tablet and desktop optimizations
- Touch-friendly interfaces
- Progressive web app capabilities

## Tenant Adaptations

### Branding and Theming
- Custom color schemes and logos
- Tenant-specific CSS overrides
- Localized fonts and typography
- Hospital-specific imagery

### Content Customization
- Tenant-specific content variants
- Localized text and media
- Regional service offerings
- Custom policies and procedures

### Feature Configuration
- Enabled/disabled features per tenant
- Custom workflows and approval processes
- Regional compliance settings
- Local integration requirements

### Data Isolation
- Tenant-specific data partitioning
- RLS policies for security
- Audit trails per tenant
- Backup and recovery per tenant

## Flowcharts (Text Format)

### Patient Appointment Booking Flow
```
[Patient] → [Homepage/Service Page]
    ↓
[Select Service] → [Choose Doctor]
    ↓
[Check Availability] → [Select Time Slot]
    ↓
[Enter Patient Info] → [Confirm Booking]
    ↓
[Receive Confirmation] → [Appointment Reminder]
    ↓
[Attend Appointment] → [Post-Visit Follow-up]
```

### Admin Content Approval Flow
```
[Content Editor] → [Create/Edit Content]
    ↓
[Save as Draft] → [Submit for Approval]
    ↓
[City Admin Review] → [Approve/Reject]
    ↓ (Approved)
[Publish Content] → [Notify Stakeholders]
    ↓ (Rejected)
[Return with Feedback] → [Content Editor Revises]
```

### Doctor Verification Flow
```
[Doctor Application] → [Upload Documents]
    ↓
[City Admin Review] → [Medical Director Approval]
    ↓ (Approved)
[Grant Permissions] → [Profile Goes Live]
    ↓ (Rejected)
[Send Feedback] → [Doctor Resubmits]
```

### Patient Care Workflow
```
[Patient Registration] → [Initial Assessment]
    ↓
[Doctor Consultation] → [Create Care Plan]
    ↓
[Schedule Appointments] → [Conduct Treatments]
    ↓
[Update Records] → [Generate Billing]
    ↓
[Process Payment] → [Patient Follow-up]
    ↓
[Discharge/Continue Care]
```

This outline provides a comprehensive foundation for the Blink Eye Hospitals platform's user experience design, ensuring scalability, customization, and compliance across multiple hospital tenants.