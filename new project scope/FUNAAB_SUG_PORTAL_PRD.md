# FUNAAB Student Union Government (SUG) Portal
## Comprehensive Platform Product Requirements Document (PRD)
**Commissioned by:** SUG Executive Body, FUNAAB (New Session Admin)  
**Prepared by:** P&P Tech  
**Version:** 3.0 (Revised Unified Scope)  
**Date:** June 26, 2026  

---

## 1. Executive Summary & Revised Scope

With the transition to the new academic session, the **FUNAAB Student Union Government (SUG)** is establishing an authoritative, centralized digital portal. This platform shifts away from stand-alone micro-directories and consolidates all official union operations under a single umbrella.

The core of the website functions as an elegant, informative introduction to the current SUG administration, their welfare vision, executive members, and active projects. 

Directly integrated into this main portal are the two major welfare initiatives:
1. **The Union Accommodation Agency (Housing Department)**
2. **The Union Transport Alliance (Logistics Department)**

Through unified "Join Welfare Partnership" gates, applicants (Student Agents, Outside Agents, and Transportation Operators) can submit official partnership registration forms. All entries automatically:
- Send a structured registration summary to the **SUG Official Mailbox**.
- Populate a unified **SUG Admin Dashboard** (the sole, centralized control dashboard of the portal) where union officials can review, approve, reject, or suspend partners.

---

## 2. Core Portal Layout & Site Architecture

The portal is designed as a modular, responsive single-page web app with sub-sections and modal panels to maintain maximum accessibility and engagement.

```text
FUNAAB SUG Portal (Single-Page Hub)
│
├── Navigation Header (Official SUG Crest, Navigation anchors, Welfare Login Button)
│
├── 1. SUG Welcome & Vision Banner (Theme: "For the Students, By the Students")
│
├── 2. Executive Council showcase (Profiles of President, Welfare Director, PRO, etc.)
│
├── 3. Welfare Initiatives Section (Interactive cards with statistics & calls-to-action)
│   ├── Accommodation Agency Details ➔ [Apply as Housing Agent Button]
│   └── Transport Alliance Details   ➔ [Apply as Transport Partner Button]
│
├── 4. Unified Application Modal Gateway (Fires corresponding dynamic HTML forms)
│
├── 5. Verification Search Bar (Public verification tool for students to check approved Agents / Drivers)
│
└── 6. Centralized SUG Admin Control Dashboard (Role-authenticated entry point)
```

---

## 3. Integrated Partnership Forms

### 3.1 Housing Agent Partnership Form
This form is filled out by individuals wishing to register as authorized representatives of the Union Accommodation Agency.

*   **Applicant Classification:** Radio toggle for `Student Agent` or `Commercial Outside Agent`.
*   **Bio Data Fields:**
    *   Full Legal Name
    *   Active WhatsApp Number & Alternate Phone Number
    *   Official Email Address
    *   Passport Photo Upload
*   **Operational & Business Information:**
    *   Registered Business Name (e.g., "Barry Housing Hub")
    *   Office Address details (State, City, Town, Street)
    *   Target Area Coverage (e.g., Camp, Cele, Oluwo, Somorin, Isolu, Harmony)
*   **Institutional Vetting Details:**
    *   *For Student Agents:* Matric Number, Department, and College.
    *   *For Outside Agents:* Marital Status check.
*   **Legitimacy Documentation Upload:**
    *   *For Individuals:* National Identification Number (NIN) Slip upload.
    *   *For Corporations:* Corporate Affairs Commission (CAC) Certificate upload.

### 3.2 Transport Operator Partnership Form
This form is filled out by local transport providers, shuttle operators, or motorcycle riders who wish to secure SUG backing to convey students.

*   **Applicant Classification:** Radio toggle for `Intra-Campus Shuttle`, `Off-Campus Cab`, or `Express Bus Operator`.
*   **Operator Bio-Data:**
    *   Full Legal Name
    *   Contact Phone / WhatsApp Number
    *   Official Email Address
    *   Passport Photo Upload
*   **Vehicle & Fleet Specifications:**
    *   Vehicle Make, Model, and Manufacture Year (e.g., Toyota Camry, Suzuki Every)
    *   Vehicle Color Configuration (Must align with FUNAAB transport guidelines if intra-campus)
    *   License Plate Number (e.g., ABJ-123-XY)
*   **Operational Details:**
    *   Route Coverage (e.g., Camp to Campus, Camp to Oluwo, Alabata Express)
    *   Operational Base Location
*   **Vetting Documentation Upload:**
    *   Driver's License file upload.
    *   Road Worthiness Certificate or Vehicle Insurance document.

---

## 4. Submission & Integration Flow

The moment an applicant clicks "Submit Application" on either form, the system handles data ingestion through two channels simultaneously:

```
                  [Form Submitted by Agent or Driver]
                                   │
                ┌──────────────────┴──────────────────┐
                ▼                                     ▼
      [Email Notification Gate]            [Database Storage Gate]
                │                                     │
    Mails structured application to       Inserts data into 'users' & 
       SUG Official Mailbox              'applicants' with status = 'pending'
                │                                     │
                └──────────────────┬──────────────────┘
                                   ▼
                   [Unified SUG Admin Dashboard]
                  (Review, Approvals, & Rejections)
```

### 4.1 Email Dispatch Specifications
The system formats the submission into a high-visibility, templated HTML email dispatched to `welfare@funaabsug.org` (or a testing mock address).
*   **Subject Line Format:** `[SUG Welfare Application] New {Agent|Transport} Registration - {Business/Applicant Name}`
*   **Email Body Content:** A clean, CSS-styled table containing all text inputs and direct link pointers to open the uploaded NIN, CAC, or Driver's License files.

### 4.2 Database Recording
The backend database script parses the payload and inserts records into the relative relational schemas, leaving their verification flags deactivated (`status = 'pending'`).

---

## 5. Unified SUG Admin Dashboard (`dashboard-admin.php`)

This is the **only dashboard** built for this session platform, serving as the command center for the SUG Executive Welfare Directorate.

### 5.1 Housing Agencies Clearance Queue
*   **Tabular View:** Displays all pending housing agent registrations.
*   **Vetting Window:** Allows admins to view matric profiles, inspect NIN slips, and review business entities.
*   **Actions:**
    *   `Approve Agent` ➔ Generates official SUG Agent ID, sends automated confirmation email, and activates their profile in the public verification pool.
    *   `Reject Agent` ➔ Marks record as rejected, prompting a text box to outline the reasons sent to the agent.
    *   `Suspend Agent` ➔ Revokes approval state instantly if infractions are observed.

### 5.2 Transport Operators Clearance Queue
*   **Tabular View:** Displays all transport operator applicants.
*   **Vetting Window:** Inspect license plate details, driver’s licenses, and operational routes.
*   **Actions:**
    *   `Approve Operator` ➔ Issues an Official SUG Transport ID (e.g., `SUG-TX-2026-004`), logs the routing permit, and updates state.
    *   `Reject Operator` ➔ Flags driver as rejected.
    *   `Suspend Operator` ➔ Revokes operating rights.

### 5.3 Unified Operations Analytics
*   Displays critical metric counters:
    *   Total Active Verified Housing Agents
    *   Total Verified Union Shuttles & Drivers
    *   Pending Submissions Queue Length
    *   Infraction Flags/Student Safety Complaints

---

## 6. Dynamic SQL Database Schema

```sql
CREATE DATABASE IF NOT EXISTS `funaab_sug_portal_db` DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
USE `funaab_sug_portal_db`;

-- 1. Core SUG Administration Accounts
CREATE TABLE IF NOT EXISTS `sug_admins` (
    `id` INT AUTO_INCREMENT PRIMARY KEY,
    `username` VARCHAR(50) UNIQUE NOT NULL,
    `password_hash` VARCHAR(255) NOT NULL,
    `role` ENUM('welfare_director', 'president_office', 'general_admin') NOT NULL,
    `last_login` TIMESTAMP NULL DEFAULT NULL
) ENGINE=InnoDB;

-- 2. Housing Agent Partners Table
CREATE TABLE IF NOT EXISTS `housing_applicants` (
    `id` INT AUTO_INCREMENT PRIMARY KEY,
    `full_name` VARCHAR(150) NOT NULL,
    `phone` VARCHAR(20) NOT NULL,
    `email` VARCHAR(100) UNIQUE NOT NULL,
    `agent_type` ENUM('student_agent', 'outside_agent') NOT NULL,
    `business_name` VARCHAR(150) NOT NULL,
    `office_address` TEXT NOT NULL,
    `target_areas` VARCHAR(255) NOT NULL, -- Comma-separated list e.g., "Camp, Cele"
    `matric_number` VARCHAR(30) DEFAULT NULL, -- Nullable for outside agents
    `department` VARCHAR(100) DEFAULT NULL,
    `college` VARCHAR(50) DEFAULT NULL,
    `doc_type` ENUM('NIN', 'CAC') NOT NULL,
    `uploaded_doc_path` VARCHAR(255) NOT NULL,
    `passport_photo` VARCHAR(255) NOT NULL,
    
    -- Status Configurations
    `assigned_agent_id` VARCHAR(50) UNIQUE DEFAULT NULL, -- Issued on approval e.g., SUG-AG-2026-0001
    `status` ENUM('pending', 'approved', 'rejected', 'suspended') DEFAULT 'pending',
    `reviewed_by` INT DEFAULT NULL,
    `reviewed_at` TIMESTAMP NULL DEFAULT NULL,
    `created_at` TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (`reviewed_by`) REFERENCES `sug_admins`(`id`) ON DELETE SET NULL
) ENGINE=InnoDB;

-- 3. Transport Partners Table
CREATE TABLE IF NOT EXISTS `transport_applicants` (
    `id` INT AUTO_INCREMENT PRIMARY KEY,
    `full_name` VARCHAR(150) NOT NULL,
    `phone` VARCHAR(20) NOT NULL,
    `email` VARCHAR(100) NOT NULL,
    `vehicle_type` ENUM('shuttle', 'cab', 'express_bus', 'motorcycle') NOT NULL,
    `vehicle_make_model` VARCHAR(150) NOT NULL,
    `license_plate` VARCHAR(30) UNIQUE NOT NULL,
    `license_file_path` VARCHAR(255) NOT NULL,
    `passport_photo` VARCHAR(255) NOT NULL,
    `route_coverage` VARCHAR(255) NOT NULL,
    
    -- Status Configurations
    `assigned_driver_id` VARCHAR(50) UNIQUE DEFAULT NULL, -- Issued on approval e.g., SUG-TX-2026-0001
    `status` ENUM('pending', 'approved', 'rejected', 'suspended') DEFAULT 'pending',
    `reviewed_by` INT DEFAULT NULL,
    `reviewed_at` TIMESTAMP NULL DEFAULT NULL,
    `created_at` TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (`reviewed_by`) REFERENCES `sug_admins`(`id`) ON DELETE SET NULL
) ENGINE=InnoDB;
```
