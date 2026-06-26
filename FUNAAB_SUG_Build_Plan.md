# FUNAAB SUG Portal — Build Plan

**Project Name:** FUNAAB SUG  
**Based on:** PRD v3.0 (FUNAAB_SUG_PORTAL_PRD.md)  
**Decided:** June 26, 2026  
**Scope:** Frontend UI only (static HTML/CSS)  

---

## 1. Project Overview

This project is a comprehensive **FUNAAB Student Union Government (SUG) Portal** that consolidates all official union operations under a single umbrella. It replaces the previous standalone Union Accommodation Agency (UAA) platform.

### Key Differences from Previous Build:
| Aspect | Previous (UAA) | New (FUNAAB SUG) |
|--------|----------------|------------------|
| **Scope** | Housing agent registration only | Housing + Transport unified |
| **Header** | Had CTA buttons (Log In, Apply as Agent) | Clean header, no buttons |
| **Dashboard** | Separate welfare/president panels | Single unified admin dashboard |
| **Verification** | Agent-only verification | Agent + Driver verification |
| **Application Flow** | Direct page navigation | Modal gateway for forms |

### Design Continuity:
- **Color Palette:** FUNAAB institutional green (#006B3F, #00A859, #003D23, #D4A843 gold)
- **Typography:** Poppins (headings), Source Sans 3 (body)
- **Layout Structure:** Same container, grid, and component patterns
- **Animations:** IntersectionObserver fade-in, counter animations

---

## 2. File Structure

```
estate/
├── FUNAAB_SUG_Build_Plan.md           (This file)
├── new project scope/
│   └── FUNAAB_SUG_PORTAL_PRD.md       (PRD v3.0)
│
├── css/
│   └── styles.css                      (To be created - shared styles)
│
├── sug-portal.html                     (Main SUG Portal landing page)
├── apply-housing.html                  (Housing Agent application form)
├── apply-transport.html                (Transport Operator application form)
├── dashboard-admin.html                (Unified Admin Dashboard)
└── verify.html                         (Public verification tool)
```

---

## 3. Page Specifications

### 3.1 Main Landing Page (`sug-portal.html`)

**Header (Clean - No CTA Buttons):**
- Logo: SUG Crest with "FUNAAB Student Union Government" text
- Navigation Links: Home, Executive Council, Welfare Initiatives, Verification, About, Contact
- Mobile hamburger menu

**Section 1: SUG Welcome & Vision Banner**
- Hero with "For the Students, By the Students" theme
- Green gradient background (same as current hero)
- Two-column: text + SUG building/campus image
- Badge: "Official SUG Portal"

**Section 2: Executive Council Showcase**
- Grid of executive members (4-column on desktop)
- Cards with: photo, name, position (President, Welfare Director, PRO, etc.)
- Hover effect with shadow lift

**Section 3: Welfare Initiatives Section**
- Section header: "Our Welfare Initiatives"
- Two interactive cards side-by-side:
  1. **Union Accommodation Agency** card
     - Icon/image
     - Brief description
     - Stats (e.g., "120+ Verified Agents")
     - Button: "Apply as Housing Agent" → navigates to `apply-housing.html`
  2. **Union Transport Alliance** card
     - Icon/image
     - Brief description
     - Stats (e.g., "50+ Verified Drivers")
     - Button: "Apply as Transport Partner" → navigates to `apply-transport.html`

**Section 4: Verification Search Bar**
- Public verification tool
- Search input: "Enter Agent ID or Driver ID"
- Search button
- Result display area (found/not-found states)

**Section 5: About / How It Works**
- Two-column layout
- Left: text describing the unified portal
- Right: step-by-step check list (5 items)

**Section 6: Footer**
- 4-column grid: SUG Info, Quick Links, Welfare Links, Contact
- Copyright: "© 2026 FUNAAB Student Union Government"
- Built by: "P&P Tech"

---

### 3.2 Housing Agent Application (`apply-housing.html`)

**Page Layout:**
- Page hero with green gradient background
- Title: "Housing Agent Partnership Registration"
- Subtitle: "Join the Union Accommodation Agency"

**Form Fields (per PRD v3.0):**

1. **Applicant Classification** (Radio toggle)
   - Student Agent
   - Commercial Outside Agent

2. **Bio Data Fields**
   - Full Legal Name (text)
   - Active WhatsApp Number (tel)
   - Alternate Phone Number (tel)
   - Official Email Address (email)
   - Passport Photo Upload (file)

3. **Operational & Business Information**
   - Registered Business Name (text)
   - Office Address — State (select/input)
   - Office Address — City (text)
   - Office Address — Town (text)
   - Office Address — Street (text)
   - Target Area Coverage (multi-select checkboxes: Camp, Cele, Oluwo, Somorin, Isolu, Harmony)

4. **Institutional Vetting Details** (conditional fields)
   - *For Student Agents:*
     - Matric Number (text)
     - Department (text)
     - College (select/text)
   - *For Outside Agents:*
     - Marital Status (select)

5. **Legitimacy Documentation Upload**
   - *For Individuals:* NIN Slip (file upload)
   - *For Corporations:* CAC Certificate (file upload)

6. **Submit Button:** "Submit Application"

---

### 3.3 Transport Operator Application (`apply-transport.html`)

**Page Layout:**
- Page hero with green gradient background
- Title: "Transport Operator Partnership Registration"
- Subtitle: "Join the Union Transport Alliance"

**Form Fields (per PRD v3.0):**

1. **Applicant Classification** (Radio toggle)
   - Intra-Campus Shuttle
   - Off-Campus Cab
   - Express Bus Operator

2. **Operator Bio-Data**
   - Full Legal Name (text)
   - Contact Phone / WhatsApp Number (tel)
   - Official Email Address (email)
   - Passport Photo Upload (file)

3. **Vehicle & Fleet Specifications**
   - Vehicle Make (text, e.g., "Toyota")
   - Vehicle Model (text, e.g., "Camry")
   - Manufacture Year (number/select)
   - Vehicle Color Configuration (text)
   - License Plate Number (text, e.g., "ABJ-123-XY")

4. **Operational Details**
   - Route Coverage (text/multi-select, e.g., "Camp to Campus, Camp to Oluwo, Alabata Express")
   - Operational Base Location (text)

5. **Vetting Documentation Upload**
   - Driver's License (file upload)
   - Road Worthiness Certificate or Vehicle Insurance (file upload)

6. **Submit Button:** "Submit Application"

---

### 3.4 Admin Dashboard (`dashboard-admin.html`)

**Layout:**
- Sidebar navigation (dark green #003D23 background)
- Main content area (off-white background)
- Same sidebar style as current `.dashboard` CSS

**Sidebar Menu:**
- Dashboard (overview)
- Housing Agents
- Transport Operators
- Analytics
- Settings (placeholder)

**Main Content Sections:**

**Section 1: Overview Header**
- Title: "SUG Admin Dashboard"
- User info (admin name, role)

**Section 2: Analytics Cards** (4 cards in a row)
- Total Active Verified Housing Agents (count)
- Total Verified Union Shuttles & Drivers (count)
- Pending Submissions Queue (count)
- Infraction Flags / Complaints (count)

**Section 3: Housing Agencies Clearance Queue**
- Tabular view of pending housing agent registrations
- Columns: Name, Type, Business, Date Applied, Status, Actions
- Actions: Approve, Reject, Suspend (button/link)
- Expandable row for viewing full details

**Section 4: Transport Operators Clearance Queue**
- Tabular view of pending transport applicants
- Columns: Name, Vehicle Type, Plate No, Route, Date Applied, Status, Actions
- Actions: Approve, Reject, Suspend (button/link)
- Expandable row for viewing full details

**Section 5: Recent Activity / History**
- Log of recent actions with timestamps

---

### 3.5 Verification Page (`verify.html`)

**Page Layout:**
- Page hero with green gradient
- Title: "Verify an Agent or Driver"
- Subtitle: "Check if an agent or driver is officially approved by the SUG"

**Search Component:**
- Search input field
- Search button: "Verify"
- Filter toggle: All / Housing Agents / Transport Operators

**Result Display:**
- **Found State:** Card with green border, showing:
  - Agent/Driver photo
  - Name
  - ID Number (e.g., SUG-AG-2026-0001 or SUG-TX-2026-001)
  - Type (Student Agent / Outside Agent / Shuttle / Cab / etc.)
  - Status badge (Approved)
  - Coverage area / Route
  
- **Not Found State:** Card with red border, showing:
  - "No Record Found" message
  - Suggestion to contact welfare office

---

## 4. CSS Strategy

### New CSS Additions Needed:
- Welfare Initiatives section cards (two-column with icon/image)
- Modal styling for application forms (if using modal gateway)
- Verification result cards (found/not-found states)
- Executive Council grid (similar to existing `.team-grid`)
- Page hero styles for application pages
- Form section styling for multi-step appearance

### Color Palette (Preserved):
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
}
```

---

## 5. Interactive Features

- Smooth scroll navigation with header offset
- Header shadow on scroll
- IntersectionObserver fade-in animations on sections
- Counter animation (for stats)
- Mobile responsive hamburger menu
- Form validation (HTML5 + optional JS)
- Radio toggle for conditional form fields

---

## 6. Implementation Order

| # | Task | File | Status |
|---|------|------|--------|
| 1 | Create project build plan | `FUNAAB_SUG_Build_Plan.md` | ✅ Complete |
| 2 | Create shared CSS styles | `css/styles.css` | ⏳ Pending |
| 3 | Main landing page | `sug-portal.html` | ⏳ Pending |
| 4 | Housing application form | `apply-housing.html` | ⏳ Pending |
| 5 | Transport application form | `apply-transport.html` | ⏳ Pending |
| 6 | Admin dashboard | `dashboard-admin.html` | ⏳ Pending |
| 7 | Verification page | `verify.html` | ⏳ Pending |
| 8 | Testing + QA | — | ⏳ Pending |

---

## 7. Notes

- **Header stays clean:** No CTA buttons in the header. Application buttons are only in the Welfare Initiatives section.
- **Modal vs Separate Pages:** The PRD mentions "Unified Application Modal Gateway" but we'll use separate pages for now (better UX for complex forms). Can convert to modals later if needed.
- **Database:** Schema defined in PRD v3.0 will be implemented in backend phase (not this sprint).
- **Email System:** Will be implemented in backend phase.

---

**Last Updated:** June 26, 2026  
**Next Steps:** Begin implementation with CSS and landing page.
