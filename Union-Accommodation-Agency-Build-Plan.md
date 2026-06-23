# Build Plan — Union Accommodation Agency Platform

**Based on:** PRD v2.0
**Decided:** June 22, 2026
**Revised:** June 22, 2026 — Minimal-plugin approach with custom PHP dashboards

---

## Open Questions — Resolved

| Question | Decision |
|----------|----------|
| **Matric number privacy** | Partially hidden on public profiles (e.g. last 4 digits only). Full number visible only to Welfare/President on the approval panel. |
| **Caretaker details** | Dropped from v1. Can be added post-launch if needed. |
| **Marital status** | Internal only — stored in agent record for Welfare office reference, never displayed on public profiles. |
| **Signature mechanism** | Pre-uploaded PNGs. Each signer uploads once; the system applies them automatically to generated PDFs. |
| **PDF downloadability** | Both — emailed automatically AND downloadable from the agent's dashboard. |
| **Resumption deadline** | Flexible — no hard date constraint. |

---

## Architecture Overview

```
┌─────────────────────────────────────────────────────┐
│                  PUBLIC PAGES (Elementor)            │
│  Home · Browse Agents · Browse Listings · Verify    │
│  How It Works · About · Contact · Register · Login  │
└──────────────────────┬──────────────────────────────┘
                       │ shortcodes / AJAX
┌──────────────────────▼──────────────────────────────┐
│              CUSTOM PHP (Code Snippets)              │
│  ┌──────────┐  ┌──────────┐  ┌──────────────────┐  │
│  │  Agent   │  │ Student  │  │  Admin/Welfare/  │  │
│  │Dashboard │  │Dashboard │  │  President Panel │  │
│  └──────────┘  └──────────┘  └──────────────────┘  │
│  Custom Post Types · User Roles · PDF Generation    │
│  Approval Workflow · Email Automation · AJAX APIs   │
└──────────────────────┬──────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────┐
│                  WORDPRESS CORE                      │
│  Users · User Meta · Options Table · Uploads        │
└─────────────────────────────────────────────────────┘
```

**Design principle:** Elementor handles visual landing/public pages. All dashboards, panels, and business logic are custom PHP injected via Code Snippets. No JetEngine, no JetSmartFilters, no ProfilePress, no WPForms. Minimal plugin footprint.

---

## Tech Stack

| Layer | Tool | Purpose |
|-------|------|---------|
| Core CMS | WordPress | Foundation |
| Theme | Hello Elementor (free) or GeneratePress | Lightweight, Elementor-compatible |
| Page builder | Elementor (free or Pro) | Public-facing landing pages only |
| Custom PHP injection | **Code Snippets (free)** | All dashboard logic, CPTs, roles, workflows |
| PDF generation | FPDF or TCPDF (PHP library, loaded via Code Snippets) | Agent registration certificates |
| Email delivery | WP Mail SMTP (free) | Automated certificate emails |
| File uploads | WordPress native `wp_handle_upload` | NIN/CAC/passport uploads |

### Plugins NOT used (and why)

| Plugin | Why excluded |
|--------|-------------|
| ProfilePress | Registration/login handled by custom PHP + `wp_create_user()` |
| JetEngine | Custom post types registered via `register_post_type()` in Code Snippets |
| JetSmartFilters | Filtering built with custom WP_Query + AJAX |
| ACF | Custom fields stored as post meta via `register_meta()` or `update_post_meta()` |
| WPForms / Fluent Forms | Forms built in Elementor or HTML, processed by custom PHP |
| Elementor Pro (optional) | Free Elementor sufficient for landing pages; Pro only needed for theme builder |

---

## WordPress Setup

### Required Plugins (keep minimal)

1. **Elementor** (free) — landing/public pages
2. **Code Snippets** (free) — all custom PHP
3. **WP Mail SMTP** (free) — email delivery

That's it. Three plugins.

### Theme
- Install **Hello Elementor** (free) or **GeneratePress** (free)
- No custom theme needed — Elementor handles all visual design

### Permalinks
- Set to `/%postname%/` (pretty URLs)

### Uploads Directory
- Create `wp-content/uploads/uaa-certificates/` for generated PDFs
- Create `wp-content/uploads/uaa-documents/` for NIN/CAC/passport uploads

---

## Custom Post Types (via Code Snippets)

All CPTs registered via `register_post_type()` in Code Snippets.

### Agent (CPT slug: `uaa_agent`)

| Field (post meta) | Type | Notes |
|-------|------|-------|
| Full Name | Text | Required |
| Passport Photo | Image URL | Stored as attachment ID |
| Phone / WhatsApp | Text | Required |
| Email | Email | Required |
| Business Name | Text | Required |
| Registration Type | Select | individual / business |
| NIN Slip | File URL | Required if individual |
| CAC Document | File URL | Required if business |
| Matric Number | Text | Student Agent only |
| Department | Text | Student Agent only |
| Marital Status | Select | Outside Agent only (internal, not public) |
| Office Address — State | Text | Required |
| Office Address — City | Text | Required |
| Office Address — Town | Text | Required |
| Office Address — Street | Text | Required |
| Agent Type | Select | student / outside |
| Status | Select | pending / awaiting_welfare / awaiting_president / approved / rejected / suspended |
| Agent ID | Text | Auto-generated on approval (format: `UAA/YYYY/NNNN`) |
| Welfare Sign-Off | Checkbox + timestamp | Yes/no + date/time |
| President Sign-Off | Checkbox + timestamp | Yes/no + date/time |
| Certificate PDF | File path | Stored after generation |
| Rejection Reason | Text | If rejected |

### House Listing (CPT slug: `uaa_listing`)

| Field (post meta) | Type | Notes |
|-------|------|-------|
| Title | Text | Required |
| Area | Taxonomy term (uaa_area) | Required |
| Price | Number | Required |
| Room Type | Select | self-contained / room-and-parlor / 1-bedroom / 2-bedroom / 3-bedroom / shared / other |
| Photos | Array of attachment IDs | Multiple images |
| Description | Textarea | Required |
| Agent | Post reference (uaa_agent) | Auto-linked to creator |

### Area (Taxonomy slug: `uaa_area`)

| Field | Type | Notes |
|-------|------|-------|
| Name | Text | e.g. "Camp", "Harmony", "Camp Junction" |
| Description | Text | Optional |
| Listing Count | Auto | Calculated dynamically |

---

## User Roles (via Code Snippets)

Registered via `add_role()` in Code Snippets.

| Role | Key | Capabilities |
|------|-----|-------------|
| **Student** | `uaa_student` | Read, edit own profile, submit reports |
| **Student Agent** | `uaa_student_agent` | Student caps + create/edit/delete own `uaa_agent` + `uaa_listing` posts |
| **Outside Agent** | `uaa_outside_agent` | Create/edit/delete own `uaa_agent` + `uaa_listing` posts |
| **SUG Director of Welfare** | `uaa_welfare` | Edit `uaa_agent` posts, manage all, access approval panel |
| **Office of the President** | `uaa_president` | Edit `uaa_agent` posts, final sign-off, access approval panel |

---

## Custom PHP Modules (Code Snippets)

Each module is a separate Code Snippets entry for organization.

### Snippet 1: CPT & Taxonomy Registration
```php
// Registers: uaa_agent, uaa_listing CPTs + uaa_area taxonomy
// Sets: labels, supports, capabilities, rewrite rules
// Registers: post meta fields for all custom fields
```

### Snippet 2: User Role Registration
```php
// Creates 5 custom roles via add_role()
// Assigns capabilities per role
// Redirects after login based on role
```

### Snippet 3: Registration Handler
```php
// Handles student, student agent, outside agent registration
// Creates WordPress user via wp_create_user()
// Creates Agent CPT entry with status 'pending'
// Stores extra profile fields as user meta
// Uploads passport photo, NIN/CAC documents
// Sends confirmation email
```

### Snippet 4: Login Handler
```php
// Custom login with email or phone + password
// Role-based redirect after login:
//   Student → /student-dashboard/
//   Agent → /agent-dashboard/
//   Welfare → /approval-panel/
//   President → /approval-panel/
// Forgot password via wp_lostpassword_url()
```

### Snippet 5: Approval Workflow
```php
// Status transitions:
//   pending → awaiting_welfare → awaiting_president → approved
// Welfare actions: approve, reject, suspend
// President actions: sign-off, reject, suspend
// Generates Agent ID on final approval (UAA/YYYY/NNNN)
// Triggers PDF generation + email on approval
// Logs all status changes with timestamps
```

### Snippet 6: Agent ID Generator
```php
// Format: UAA/YYYY/NNNN (e.g. UAA/2026/0001)
// Sequential numbering stored in wp_options
// Runs once on final approval
// Immutable once assigned
```

### Snippet 7: PDF Certificate Generator
```php
// Uses FPDF or TCPDF (loaded manually or via Composer in wp-content)
// Triggered by hook when status → approved
// Pulls agent meta: name, business name, Agent ID, registration type, matric/dept, address
// Places pre-uploaded signature PNGs at bottom
// Saves PDF to wp-content/uploads/uaa-certificates/
// Stores file path in agent post meta
// Sends email via WP Mail SMTP with PDF attachment
// Returns file path for dashboard download
```

### Snippet 8: Email Automation
```php
// Functions:
//   uaa_send_certificate_email($agent_id) — triggered on approval
//   uaa_send_status_update($agent_id, $new_status) — on any status change
//   uaa_send_report_notification($report_id) — on scam report submission
// Uses wp_mail() configured via WP Mail SMTP
```

### Snippet 9: AJAX API Endpoints
```php
// wp_ajax_uaa_search_agents — search agents for verify tool
// wp_ajax_uaa_filter_listings — filter listings by area/price/room type
// wp_ajax_uaa_dashboard_action — handle dashboard CRUD operations
// wp_ajax_nopriv_uaa_search_agents — public verify tool
// wp_ajax_nopriv_uaa_filter_listings — public listing filter
```

### Snippet 10: Dashboard Page Rendering
```php
// Outputs HTML for each dashboard based on user role:
//   /agent-dashboard/ — agent profile, listings, certificate
//   /student-dashboard/ — account info, reports
//   /approval-panel/ — welfare + president panels
// Uses output buffering to render HTML within shortcodes
// Styled with inline CSS or a dedicated stylesheet enqueued conditionally
```

### Snippet 11: Shortcodes
```php
// [uaa_agent_dashboard] — renders agent dashboard
// [uaa_student_dashboard] — renders student dashboard
// [uaa_approval_panel] — renders welfare/president approval panel
// [uaa_verify_form] — renders agent verification search form
// [uaa_register_student] — renders student registration form
// [uaa_register_agent] — renders agent registration form
// [uaa_login_form] — renders login form
// [uaa_report_form] — renders scam report form
```

### Snippet 12: File Upload Handler
```php
// Handles passport photo, NIN/CAC document uploads
// Validates file type (jpg, png, pdf)
// Validates file size (max 5MB)
// Saves to wp-content/uploads/uaa-documents/
// Returns attachment URL for storage in post meta
```

---

## Dashboard Interfaces

### Agent Dashboard (`/agent-dashboard/`)

**Page setup:** Elementor page with `[uaa_agent_dashboard]` shortcode.

**Sections rendered by PHP:**
1. **Profile Header** — passport photo, name, Agent ID, status badge
2. **Approval Status** — visual progress: pending → welfare → president → approved
3. **My Listings** — table/grid of own listings with edit/delete actions
4. **Create Listing** — form: title, area, price, room type, photos, description
5. **Certificate** — view/download registration confirmation PDF (if approved)
6. **Edit Profile** — update phone, email, business name, office address

**PHP functions:**
- `uaa_agent_get_profile($user_id)` — fetch agent data
- `uaa_agent_get_listings($user_id)` — fetch own listings
- `uaa_agent_create_listing($data)` — insert listing CPT
- `uaa_agent_edit_listing($id, $data)` — update listing
- `uaa_agent_delete_listing($id)` — delete listing
- `uaa_agent_download_certificate($user_id)` — serve PDF file

---

### Student Dashboard (`/student-dashboard/`)

**Page setup:** Elementor page with `[uaa_student_dashboard]` shortcode.

**Sections rendered by PHP:**
1. **Profile Header** — name, school, matric number, department
2. **My Reports** — list of submitted scam reports with status
3. **Saved Agents** — (stretch goal) bookmarked agents
4. **Edit Profile** — update phone, email, passport photo

**PHP functions:**
- `uaa_student_get_profile($user_id)` — fetch student data
- `uaa_student_get_reports($user_id)` — fetch own reports
- `uaa_student_submit_report($data)` — insert report entry

---

### Approval Panel (`/approval-panel/`)

**Page setup:** Elementor page with `[uaa_approval_panel]` shortcode.

**Sections rendered by PHP (role-based visibility):**

**Director of Welfare sees:**
1. **Pending Applications** — list of agents with status `pending` or `awaiting_welfare`
2. **Review Application** — full agent details, NIN/CAC document viewer, passport photo
3. **Actions** — Approve (→ awaiting_president) / Reject (with reason) / Suspend
4. **History** — log of all past actions with timestamps

**Office of the President sees:**
1. **Awaiting Sign-Off** — list of agents with status `awaiting_president`
2. **Review Application** — full details, welfare sign-off status
3. **Actions** — Sign Off (→ approved, triggers PDF + email) / Reject / Suspend
4. **History** — log of all past actions with timestamps

**PHP functions:**
- `uaa_welfare_get_pending()` — fetch pending applications
- `uaa_welfare_approve($agent_id)` — set welfare sign-off, forward to president
- `uaa_welfare_reject($agent_id, $reason)` — reject with reason
- `uaa_president_get_pending()` — fetch awaiting sign-off
- `uaa_president_signoff($agent_id)` — final approval, trigger PDF + email
- `uaa_president_reject($agent_id, $reason)` — reject with reason
- `uaa_suspend_agent($agent_id, $reason)` — suspend from either role
- `uaa_get_approval_history($agent_id)` — fetch status log

---

## Elementor Landing Pages

All public-facing pages built in Elementor. No custom PHP needed for these.

### Page Map

| Page | URL | Elementor Content |
|------|-----|-------------------|
| Home | `/` | Hero, search panel, featured listings, coverage areas, why us, about, stats, CTA |
| Browse Agents | `/browse-agents/` | Agent directory grid + custom filter form (AJAX) |
| Agent Profile | `/agent/{slug}/` | Single agent details (dynamic via query var) |
| Browse Listings | `/browse-listings/` | Listings directory grid + custom filter form (AJAX) |
| Listing Detail | `/listing/{slug}/` | Single listing details (dynamic via query var) |
| Verify Agent | `/verify-agent/` | `[uaa_verify_form]` shortcode |
| How It Works | `/how-it-works/` | Static content with steps |
| About | `/about/` | Static content |
| Contact | `/contact/` | Contact form (Elementor form widget) + office details |
| Register as Agent | `/register/` | `[uaa_register_agent]` shortcode |
| Student Sign Up | `/student-signup/` | `[uaa_register_student]` shortcode |
| Login | `/login/` | `[uaa_login_form]` shortcode |
| Report Scam | `/report-scam/` | `[uaa_report_form]` shortcode |

### Dynamic Content in Elementor

For pages that need dynamic data (Agent Profile, Listing Detail), use:
- **Elementor Pro** dynamic tags with custom queries, OR
- **Custom PHP shortcodes** that output the HTML directly (simpler, no Pro needed)

Example: Agent Profile page uses `[uaa_agent_profile]` shortcode that reads the slug from the URL and renders the full profile.

---

## Custom CSS Strategy

### Landing Pages (Elementor)
- Style via Elementor's visual editor
- Global colors set in Elementor to match FUNAAB green palette
- Additional custom CSS in Elementor's custom CSS tab or `style.css`

### Dashboards (Custom PHP)
- Dedicated stylesheet: `wp-content/themes/hello-elementor/child-theme/uaa-dashboards.css`
- Enqueued conditionally only on dashboard pages
- Contains all dashboard-specific styles (cards, tables, forms, status badges)
- Responsive breakpoints at 980px and 600px

### Shared Variables
```css
:root {
  --green-primary: #006B3F;
  --green-bright: #00A859;
  --green-dark: #003D23;
  --green-deep: #005531;
  --green-tint: #E8F5EE;
  --gold: #D4A843;
  --ink: #1A2B23;
  --muted: #5E6E64;
  --white: #FFFFFF;
  --off-white: #F7FAF8;
  --line: #D6E5DC;
  --danger: #C0392B;
}
```

---

## Implementation Order

| # | Task | Phase | Module |
|---|------|-------|--------|
| 1 | WordPress + theme + 3 plugins install | Foundation | — |
| 2 | CPT + taxonomy registration | Foundation | Snippet 1 |
| 3 | User roles setup | Foundation | Snippet 2 |
| 4 | Registration handler + forms | Auth | Snippet 3 |
| 5 | Login handler + redirects | Auth | Snippet 4 |
| 6 | File upload handler | Auth | Snippet 12 |
| 7 | Approval workflow (Welfare + President) | Core Logic | Snippet 5 |
| 8 | Agent ID generation | Core Logic | Snippet 6 |
| 9 | PDF certificate generation | Core Logic | Snippet 7 |
| 10 | Email automation | Core Logic | Snippet 8 |
| 11 | AJAX API endpoints | Core Logic | Snippet 9 |
| 12 | Shortcodes registration | Dashboards | Snippet 11 |
| 13 | Dashboard page rendering | Dashboards | Snippet 10 |
| 14 | Agent dashboard UI | Dashboards | Snippet 10 |
| 15 | Student dashboard UI | Dashboards | Snippet 10 |
| 16 | Approval panel UI | Dashboards | Snippet 10 |
| 17 | Dashboard CSS | Dashboards | Stylesheet |
| 18 | Elementor landing pages (Home, Browse, Profile, Listings, etc.) | Public Pages | Elementor |
| 19 | Verify agent tool (AJAX) | Public Pages | Snippet 9 |
| 20 | Scam report form | Public Pages | Snippet 3 |
| 21 | Responsive styling + polish | Polish | — |
| 22 | Testing + QA | Polish | — |

---

## File Structure

```
wp-content/
├── themes/
│   └── hello-elementor/
│       ├── style.css                    (theme overrides)
│       └── child-theme/
│           └── uaa-dashboards.css       (dashboard styles)
├── uploads/
│   ├── uaa-certificates/               (generated PDFs)
│   └── uaa-documents/                  (NIN, CAC, passport uploads)
├── plugins/
│   ├── elementor/
│   ├── code-snippets/
│   └── wp-mail-smtp/
└── snippets/
    ├── 01-cpt-taxonomy.php
    ├── 02-user-roles.php
    ├── 03-registration.php
    ├── 04-login.php
    ├── 05-approval-workflow.php
    ├── 06-agent-id-generator.php
    ├── 07-pdf-certificate.php
    ├── 08-email-automation.php
    ├── 09-ajax-api.php
    ├── 10-dashboard-rendering.php
    ├── 11-shortcodes.php
    └── 12-file-uploads.php
```

---

## Success Metrics

- Number of agents registered and fully approved before resumption
- Number of listings published at launch
- Time from application submission to both sign-offs
- Reduction in scam reports over first semester
- Welfare and President offices able to manage approvals without developer intervention
