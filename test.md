---
id: root-cause-analytics-sap-incidents
title: "Build an Enterprise Grade UI5 App Live in 60 Minutes"
description: "Use AI-assisted development with Cline to build a professional SAP UI5 Fiori-style analytics dashboard with incident management, root cause analysis, and AI-powered recommendations."
version: "1.0.0"
author:
  name: "Author Name"
  email: "author@example.com"
difficulty: beginner
estimated_duration: "60 minutes"
category: "Cloud Development"
tags: ["SAP UI5", "Fiori", "Cline", "AI-assisted development", "SAP BTP", "incident management"]
prerequisites:
  - title: "Basic familiarity with VS Code"
  - title: "Access to SAP GitHub Enterprise (github.tools.sap)"
  - title: "Internet connection for package downloads"
learning_objectives:
  - "Set up Cline with the HAI proxy for AI-assisted development"
  - "Craft effective prompts for UI5 application generation"
  - "Build a professional SAP Fiori dashboard using sap.m and sap.ui.layout libraries"
  - "Implement routing and navigation between multiple UI5 pages"
  - "Create analytics insight cards, filterable tables, and an AI recommendation interface"
ai_context: |
  This guide walks learners through building a full-featured Root Cause Analytics for SAP Incidents application using SAP UI5 and Cline (an AI coding assistant). Learners use prompt-driven development — copying carefully crafted prompts into Cline — to generate a Fiori-style dashboard with KPI cards, incident tables, analytics cards, and an AI recommendations interface. The guide covers environment setup (Node.js, HAI proxy, Cline, UI5 MCP Server), four iterative build steps, and a final verification checklist.
---

# Root Cause Analytics for SAP Incidents

> **Summary**: Use AI-assisted development with Cline to build a professional SAP UI5 Fiori-style analytics dashboard with incident management, root cause analysis, and AI-powered recommendations — all in under 60 minutes!

## Introduction

In this hands-on guide, you'll use **Cline** (an AI coding assistant in VS Code) to build a full-featured **Root Cause Analytics for SAP Incidents** application using SAP UI5. Instead of writing code manually, you'll feed carefully crafted prompts to Cline and watch it generate a production-quality Fiori dashboard step by step.

### What You'll Build
- A professional SAP Fiori dashboard home page with KPI cards and navigation tiles
- An Incident Overview page with filterable incident tables and colored priority indicators
- A Root Cause Analytics page with analytics insight cards and resolution metrics
- An AI Recommendations page with text-based incident analysis and predicted root causes

### Architecture Overview

```
┌─────────────────────────────────────────────────────┐
│                   Dashboard Home                    │
│  ┌──────────┬──────────┬──────────┬──────────┐      │
│  │ Open (24)│Crit (7)  │Systems(5)│Resolved  │      │
│  │          │          │          │  (12)    │      │
│  └──────────┴──────────┴──────────┴──────────┘      │
│  ┌──────────────┬──────────────┬──────────────┐     │
│  │  Incident    │  Root Cause  │     AI       │     │
│  │  Overview    │  Analytics   │  Recommend.  │     │
│  └──────┬───────┴──────┬───────┴──────┬───────┘     │
│         │              │              │             │
│         ▼              ▼              ▼             │
│    ┌─────────┐   ┌─────────┐   ┌─────────┐          │
│    │ Table + │   │Analytics│   │ Text +   │         │
│    │ Filters │   │ Cards   │   │ Analysis │         │
│    └─────────┘   └─────────┘   └─────────┘          │
└─────────────────────────────────────────────────────┘
```

---

<!-- step:1 -->
## Step 1: Set Up Your Development Environment

> **Duration**: ~15 minutes
> **Difficulty**: beginner

### Objective
Install and configure all the required tools. 

### Instructions

#### 🔧 Configure Cline in VS Code (Both Platforms)

1. Open **VS Code** → Go to **Extensions** → Search for **"Cline"** → Click **Install**.

2. Open Cline,For this session, the gen-ai keys are already configured in the system. 

3. Test the connection by sending a simple message like "Hello" in the Cline chat.

4. Create an empty folder and open it in VS Code.

---

#### 🔌 Add the UI5 MCP Server to Cline

The **UI5 MCP Server** provides Cline with specialized tools for UI5 development — including guidelines enforcement, API reference lookups, linting, and project scaffolding. This is **essential** for generating high-quality UI5 code.

4. Open the **Cline MCP Settings** file:
    - In VS Code, open the Command Palette (`Cmd+Shift+P` on macOS / `Ctrl+Shift+P` on Windows)
    - Type **"Cline: MCP Settings"** and select it

5. Add the following MCP server configuration to the `mcpServers` section:

```json title="MCP Server Configuration"
{
  "mcpServers": {
    "@ui5/mcp-server": {
      "autoApprove": [],
      "disabled": false,
      "timeout": 60,
      "type": "stdio",
      "command": "npx",
      "args": [
        "@ui5/mcp-server"
      ]
    }
  }
}
```

6. **Save** the file. Cline will automatically detect the new MCP server.

7. **Verify** the MCP server is connected by opening the Cline chat panel — you should see `@ui5/mcp-server` listed as a connected server. The server provides these tools:
    - `get_guidelines` — Retrieves UI5 best practices (called automatically before UI5 tasks)
    - `run_ui5_linter` — Lints your UI5 project for deprecated APIs and issues
    - `get_api_reference` — Looks up UI5 API documentation
    - `get_project_info` — Analyzes your UI5 project structure
    - `create_ui5_app` — Scaffolds new UI5 applications
    - `run_manifest_validation` — Validates your `manifest.json`

> [!TIP]
> With the UI5 MCP server enabled, Cline will automatically call `get_guidelines` before working on any UI5 task, ensuring the generated code follows SAP best practices. It will also be able to lint your project and look up API references on the fly.

---

#### 🧪 Final Validation

Run these commands in system terminal (command prompt) as administrator to verify everything is ready:

**macOS:**
```bash terminal title="Validate All Tools (macOS)"
node --version && npm --version
```

**Windows (PowerShell):**
```powershell terminal title="Validate All Tools (Windows)"
node --version; npm --version;
```

Expected output: Version numbers for all three tools.

### Checkpoint
- [ ] `node --version` returns a version number
- [ ] `npm --version` returns a version number
- [ ] Cline is installed in VS Code
- [ ] `@ui5/mcp-server` appears as a connected MCP server in Cline
- [ ] Sending "Hello" in Cline chat returns a response
<!-- /step:1 -->

---

<!-- step:2 -->
## Step 2: Build the Dashboard Home Page

> **Duration**: ~15 minutes
> **Difficulty**: beginner

### Objective
Use Cline to generate a professional SAP Fiori dashboard home page with a header area, KPI cards, quick navigation tiles, and a recent incidents panel.

### Instructions

1. Open the **Cline chat panel** in VS Code.

2. Copy and paste the following prompt into Cline and press Enter:

```text title="Cline Prompt — Dashboard Home Page"
Create a SAP UI5 Fiori-style application called "Root Cause Analytics for SAP Incidents".

Build a professional SAP Fiori dashboard home page similar to SAP monitoring or analytics applications, not a simple centered page with three tiles.

The page must have structured sections that fill the screen width and reduce empty space.

Use SAP UI5 controls from sap.m and sap.ui.layout libraries only.
Do not use sap.f or sap.viz.

**Page Layout**

Use a vertical layout with full width sections so the page looks like a real dashboard.
The screen should contain four major sections.

**Section 1 — Header Area**

Create a top header area spanning the full width of the screen.

Left side:
- Title: `Root Cause Analytics for SAP Incidents`
- Below the title display a short description: `Monitor SAP system incidents, analyze root causes, and get intelligent AI-powered recommendations.`

Right side of the header should contain two action buttons:
- `Refresh Data`
- `View Alerts`

Ensure this header looks like a typical SAP Fiori page header.

**Section 2 — KPI Dashboard**

Below the header create a KPI dashboard row containing four KPI cards.

Each KPI card should contain:
- Icon
- Large number
- Label

KPIs:
- Open Incidents — 24 — icon: `sap-icon://alert`
- Critical Incidents — 7 — icon: `sap-icon://error`
- Systems Affected — 5 — icon: `sap-icon://database`
- Resolved Today — 12 — icon: `sap-icon://accept`

The KPI cards must:
- appear in one horizontal row
- have shadows
- have hover highlight effects
- look like analytics widgets

**Section 3 — Quick Navigation**

Below the KPI cards create a Quick Navigation section.

Title: `Quick Navigation`

Below the title display three large navigation cards:

- **Navigation Card 1** — Title: `Incident Overview` — Description: `View and manage SAP incidents` — Icon: `sap-icon://alert`
- **Navigation Card 2** — Title: `Root Cause Analytics` — Description: `Analyze root causes and patterns` — Icon: `sap-icon://analytics`
- **Navigation Card 3** — Title: `AI Recommendations` — Description: `Get recommended solutions for incidents` — Icon: `sap-icon://machine-learning`

These navigation cards must:
- appear in a horizontal row
- have shadows and rounded corners
- slightly elevate on hover
- trigger press events

Example console output: `Opening Incident Overview`

**Section 4 — Recent Incidents Panel**

Below the navigation cards create a Recent Incidents panel.
Display a list of 5 recent incidents.

Each row should contain:
- Incident ID
- System
- Priority
- Status

Example rows:
- INC1023 — S4HANA — High — Open
- INC1022 — BTP — Medium — Investigating
- INC1021 — Ariba — Critical — Escalated
- INC1020 — SuccessFactors — Low — Resolved
- INC1019 — S4HANA — Medium — Open

Add status icons or colored indicators.

**Layout Requirements**

The dashboard must:
- use full page width
- avoid large empty spaces
- have proper spacing between sections
- look like a real enterprise monitoring dashboard

**Interaction**

- Pressing navigation cards should log messages such as: `Opening Root Cause Analytics`
- Pressing the refresh button should log: `Refreshing dashboard data`

**Important Technical Constraint**

Only include these UI5 libraries:
- `sap.ui.core`
- `sap.m`
- `sap.ui.layout`

Do **not** include:
- `sap.f`
- `sap.viz`

This prevents errors like:
```
- `Failed to load sap/f/library.js`
- `Failed to load sap/viz/library.js`
```
```

3. Wait for Cline to finish generating. It will create or modify the following files:
   - `webapp/view/Main.view.xml` — Dashboard layout with all four sections
   - `webapp/controller/Main.controller.js` — Event handlers for buttons and navigation cards
   - `webapp/css/style.css` — Custom styles for KPI cards, navigation tiles, hover effects, and shadows
   - `webapp/manifest.json` — Application configuration and routing
   - `webapp/Component.js` — Application component with router initialization

4. Once Cline finishes, run the application:

```bash terminal title="Serve the Application"
npx ui5 serve -o index.html
```
In Case of any errors or blank page, ask Cline to fix it.

5. Open your browser and verify the following:

| Element | What to Check |
|---------|---------------|
| Header | Title and description visible on the left, Refresh/Alerts buttons on the right |
| KPI Cards | 4 cards in a horizontal row with icons, numbers, and labels |
| Navigation Cards | 3 cards with icons, titles, descriptions; hover effect works |
| Recent Incidents | Table/list with 5 incidents showing ID, system, priority, status |
| Console Output | Clicking navigation cards logs messages like `Opening Incident Overview` |
| No Errors | Browser console shows no `Failed to load` errors for sap.f or sap.viz |

### Checkpoint

**Header Area:**
- [ ] Title "Root Cause Analytics for SAP Incidents" is displayed
- [ ] Description text is visible below the title
- [ ] "Refresh Data" button is present and functional
- [ ] "View Alerts" button is present and functional

**KPI Dashboard:**
- [ ] 4 KPI cards appear in one horizontal row
- [ ] Open Incidents card shows 24 with alert icon
- [ ] Critical Incidents card shows 7 with error icon
- [ ] Systems Affected card shows 5 with database icon
- [ ] Resolved Today card shows 12 with accept icon
- [ ] Cards have shadow effects
- [ ] Cards have hover highlight effects

**Quick Navigation:**
- [ ] "Quick Navigation" section title is visible
- [ ] Incident Overview card with alert icon is present
- [ ] Root Cause Analytics card with analytics icon is present
- [ ] AI Recommendations card with machine-learning icon is present
- [ ] Cards have shadows and rounded corners
- [ ] Cards elevate slightly on hover
- [ ] Clicking cards logs messages to the console

**Recent Incidents Panel:**
- [ ] Panel displays 5 recent incidents
- [ ] Each row shows Incident ID, System, Priority, and Status
- [ ] Priority indicators have colors (Critical=Red, High=Orange, Medium=Blue, Low=Green)

**Technical:**
- [ ] No `sap.f` or `sap.viz` library errors in browser console
- [ ] Dashboard uses full page width with minimal empty space
- [ ] Application runs without errors
<!-- /step:2 -->

---

<!-- step:3 -->
## Step 3: Add Navigation and the Incident Overview Page

> **Duration**: ~20 minutes
> **Difficulty**: beginner

### Objective
Extend the dashboard with working navigation so clicking the Quick Navigation cards routes to dedicated pages, then enhance the Incident Overview page with a fully functional incident table, filters, and visual priority/status indicators.

### Instructions

#### Part A — Add Navigation and Pages

1. Open the **Cline chat panel** in VS Code.

2. Copy and paste the following prompt into Cline:

```text title="Cline Prompt — Navigation and Pages"
Extend the existing dashboard home page and implement navigation from the Quick Navigation cards.

**Navigation behavior should be:**
- Incident Overview card → Incident Overview page
- Root Cause Analytics card → Root Cause Analytics page
- AI Recommendations card → AI Recommendations page

**Page Layout Requirements**

Each page should follow SAP Fiori page design.
Each page must contain:
- Page title
- Back button
- Content section

**Incident Overview Page**
- Title: `Incident Overview`
- Description: `View and monitor SAP system incidents across different systems.`
- Content area should contain a placeholder section where incidents will be displayed later.

**Root Cause Analytics Page**
- Title: `Root Cause Analytics`
- Description: `Analyze common root causes of SAP system incidents and identify trends.`
- Content area should contain placeholder panels for analytics insights.

**AI Recommendations Page**
- Title: `AI Recommendations`
- Description: `Enter incident information to receive AI-powered recommendations for possible fixes.`
- Content area should contain a placeholder panel where the AI analysis UI will appear later.

**Navigation Behavior**

When navigation cards are clicked, console messages should appear such as:
- `Opening Incident Overview`
- `Opening Root Cause Analytics`
- `Opening AI Recommendations`

Routing must work correctly so all pages load without errors.
```

3. Wait for Cline to finish. It will create or modify:
   - `webapp/manifest.json` — Updated routing configuration with new routes and targets
   - `webapp/view/IncidentOverview.view.xml` — Incident Overview page with placeholder content
   - `webapp/view/RootCauseAnalytics.view.xml` — Root Cause Analytics page with placeholder content
   - `webapp/view/AIRecommendations.view.xml` — AI Recommendations page with placeholder content
   - `webapp/controller/IncidentOverview.controller.js` — Controller with back navigation
   - `webapp/controller/RootCauseAnalytics.controller.js` — Controller with back navigation
   - `webapp/controller/AIRecommendations.controller.js` — Controller with back navigation
   - `webapp/controller/Main.controller.js` — Updated with navigation logic

4. Verify navigation is working:

| Element | What to Check |
|---------|---------------|
| Navigation | Clicking "Incident Overview" card navigates to Incident Overview page |
| Navigation | Clicking "Root Cause Analytics" card navigates to Root Cause Analytics page |
| Navigation | Clicking "AI Recommendations" card navigates to AI Recommendations page |
| Back Button | Each page has a back button that returns to the dashboard |
| Page Content | Each page shows its title, description, and placeholder content |
| Console | Navigation logs appear (e.g., `Opening Incident Overview`) |
| No Errors | No routing errors in the browser console |

---

#### Part B — Implement the Incident Overview Page

5. Copy and paste the following prompt into Cline:

```text title="Cline Prompt — Incident Overview Page"
Enhance the Incident Overview page to display real content.

**Incident Table**

Display a Fiori-style table showing SAP incidents.

Columns:
- Incident ID
- System
- Priority
- Status
- Created Date

**Sample Data**

Example incidents:
- INC1023 — S4HANA — High — Open — 2026-03-06
- INC1022 — BTP — Medium — Investigating — 2026-03-05
- INC1021 — Ariba — Critical — Escalated — 2026-03-05
- INC1020 — SuccessFactors — Low — Resolved — 2026-03-04
- INC1019 — S4HANA — Medium — Open — 2026-03-03

**Filtering Options**

Add filters above the table:
- Filter by Priority
- Filter by System

Example systems:
- S4HANA
- BTP
- Ariba
- SuccessFactors

**Visual Enhancements**

Priority should have colored indicators:
- Critical → Red
- High → Orange
- Medium → Blue
- Low → Green

Status should include icons.
```

6. Wait for Cline to finish. It will create or modify:
   - `webapp/view/IncidentOverview.view.xml` — Full table layout with columns and filter controls
   - `webapp/controller/IncidentOverview.controller.js` — JSON model with sample data, filter logic, and formatting
   - `webapp/css/style.css` — Additional styles for priority color indicators

7. Verify the Incident Overview page in your browser:

| Element | What to Check |
|---------|---------------|
| Table | 5 incidents displayed with all columns (ID, System, Priority, Status, Date) |
| Priority Colors | Critical=Red, High=Orange, Medium=Blue, Low=Green |
| Status Icons | Status column shows appropriate icons |
| Priority Filter | Dropdown/select to filter by priority works |
| System Filter | Dropdown/select to filter by system works |
| Filtering | Selecting a filter updates the table correctly |
| Back Button | Returns to the dashboard |

### Checkpoint

**Navigation:**
- [ ] Clicking "Incident Overview" navigates to the Incident Overview page
- [ ] Clicking "Root Cause Analytics" navigates to the Root Cause Analytics page
- [ ] Clicking "AI Recommendations" navigates to the AI Recommendations page
- [ ] Each page has a working back button that returns to the dashboard

**Incident Table:**
- [ ] Table displays 5 sample incidents
- [ ] Incident ID column shows INC1023, INC1022, INC1021, INC1020, INC1019
- [ ] System column shows S4HANA, BTP, Ariba, SuccessFactors
- [ ] Priority column is visible with values Critical, High, Medium, Low
- [ ] Status column shows Open, Investigating, Escalated, Resolved
- [ ] Created Date column shows dates

**Priority Color Indicators:**
- [ ] Critical priority shows Red indicator
- [ ] High priority shows Orange indicator
- [ ] Medium priority shows Blue indicator
- [ ] Low priority shows Green indicator

**Status Icons:**
- [ ] Status values have corresponding icons

**Filters:**
- [ ] Priority filter dropdown is present above the table
- [ ] System filter dropdown is present above the table
- [ ] Filtering by "High" priority shows only High priority incidents
- [ ] Filtering by "S4HANA" system shows only S4HANA incidents
- [ ] Clearing filters shows all incidents again
<!-- /step:3 -->

---

<!-- step:4 -->
## Step 4: Implement Root Cause Analytics and AI Recommendations

> **Duration**: ~15 minutes
> **Difficulty**: beginner

### Objective
Complete the application by adding analytics insight cards to the Root Cause Analytics page and building the AI recommendation interface.

### Instructions

1. Open the **Cline chat panel** in VS Code.

2. Copy and paste the following prompt into Cline:

```text title="Cline Prompt — Root Cause Analytics and AI Recommendations"
Enhance the remaining pages with analytics insights and AI recommendation UI.

**Root Cause Analytics Page**

Display analytics insights about incidents.
Create three analytics cards.

**Card 1 — Most Common Root Causes**
- Configuration Issue — 40%
- Integration Failure — 25%
- Authorization Issue — 20%
- Data Inconsistency — 10%
- System Failure — 5%

**Card 2 — Systems with Most Incidents**
- S4HANA — 12 incidents
- BTP — 7 incidents
- Ariba — 4 incidents
- SuccessFactors — 3 incidents

**Card 3 — Resolution Insights**
- Average resolution time: 3.4 hours
- Fastest resolution: 45 minutes
- Longest resolution: 11 hours

**AI Recommendations Page**

Create an interface where users can analyze an incident description.

**UI Components:**
- Incident Description Text Area
- Analyze Incident Button
- Recommendation Panel

**Example Interaction:**

User enters: `"User cannot access purchase order module in S4HANA."`

When the Analyze button is clicked display:
- **Predicted Root Cause:** Authorization Issue
- **Recommended Fix:** Check role assignments and authorization objects for the affected user.
- **Confidence Score:** 87%

**Important Technical Rule (Prevents Errors)**

Always include this constraint:

Only use UI5 libraries:
- `sap.ui.core`
- `sap.m`
- `sap.ui.layout`

Do **not** include:
- `sap.f`
- `sap.viz`

This prevents the error:
```
Failed to load sap/f/library.js
Failed to load sap/viz/library.js
```
```

3. Wait for Cline to finish. It will create or modify:
   - `webapp/view/RootCauseAnalytics.view.xml` — Three analytics cards with data
   - `webapp/controller/RootCauseAnalytics.controller.js` — JSON model with analytics data
   - `webapp/view/AIRecommendations.view.xml` — Text area, analyze button, and recommendation panel
   - `webapp/controller/AIRecommendations.controller.js` — Analysis logic with simulated AI response
   - `webapp/css/style.css` — Styles for analytics cards and recommendation panel

4. Run the application and do a final end-to-end verification:

```bash terminal title="Serve the Application"
npx ui5 serve -o index.html
```

5. Verify the Root Cause Analytics and AI Recommendations pages:

| Element | What to Check |
|---------|---------------|
| Root Cause Card | Shows 5 root causes with percentages (Configuration Issue 40%, etc.) |
| Systems Card | Shows 4 systems with incident counts |
| Resolution Card | Shows average, fastest, and longest resolution times |
| Text Area | AI page has a text input area for incident description |
| Analyze Button | "Analyze Incident" button is present |
| AI Results | After clicking Analyze, predicted root cause, fix, and confidence appear |
| No Errors | No library loading errors in the console |

> [!TIP]
> To test the AI Recommendations page, type `"User cannot access purchase order module in S4HANA."` into the text area and click **Analyze Incident**. You should see the Predicted Root Cause, Recommended Fix, and Confidence Score appear in the recommendation panel.

### Checkpoint

**Root Cause Analytics — Card 1 (Most Common Root Causes):**
- [ ] Configuration Issue — 40% is displayed
- [ ] Integration Failure — 25% is displayed
- [ ] Authorization Issue — 20% is displayed
- [ ] Data Inconsistency — 10% is displayed
- [ ] System Failure — 5% is displayed

**Root Cause Analytics — Card 2 (Systems with Most Incidents):**
- [ ] S4HANA — 12 incidents
- [ ] BTP — 7 incidents
- [ ] Ariba — 4 incidents
- [ ] SuccessFactors — 3 incidents

**Root Cause Analytics — Card 3 (Resolution Insights):**
- [ ] Average resolution time: 3.4 hours
- [ ] Fastest resolution: 45 minutes
- [ ] Longest resolution: 11 hours

**AI Recommendations — UI Components:**
- [ ] Incident Description text area is present
- [ ] "Analyze Incident" button is present
- [ ] Recommendation panel area is visible

**AI Recommendations — Analysis Interaction:**
- [ ] Can type incident description in the text area
- [ ] Clicking "Analyze Incident" triggers the analysis
- [ ] Predicted Root Cause "Authorization Issue" is displayed
- [ ] Recommended Fix is displayed with actionable text
- [ ] Confidence Score "87%" is displayed

**Technical:**
- [ ] No `sap.f` or `sap.viz` errors in browser console
- [ ] Back button works on both pages
- [ ] All navigation between pages works correctly
<!-- /step:4 -->

---

<!-- summary -->
## Summary

### 🎉 Congratulations!
You've successfully built a full-featured **Root Cause Analytics for SAP Incidents** application using AI-assisted development with Cline!

### What You Accomplished
- ✅ Set up a development environment with Node.js, HAI proxy, and Cline
- ✅ Built a professional SAP Fiori dashboard with KPI cards and navigation tiles
- ✅ Implemented routing between multiple pages
- ✅ Created an Incident Overview page with filterable tables and colored indicators
- ✅ Built Root Cause Analytics cards with percentage breakdowns and metrics
- ✅ Developed an AI Recommendations interface with simulated analysis

### Final Application Verification

Run the application one final time and verify end-to-end:

```bash terminal title="Final Verification"
npx ui5 serve -o index.html
```

| Page | Status |
|------|--------|
| Dashboard Home | KPI cards, navigation tiles, recent incidents all visible |
| Incident Overview | Table with 5 incidents, filters work, colors correct |
| Root Cause Analytics | 3 analytics cards with correct data |
| AI Recommendations | Text area + Analyze button + results panel works |
| Navigation | All routes work, back buttons return to dashboard |
| Console | No errors related to `sap.f` or `sap.viz` |

### Project Structure

```
project2/
├── package.json
├── ui5.yaml
├── webapp/
│   ├── Component.js
│   ├── manifest.json
│   ├── index.html
│   ├── controller/
│   │   ├── App.controller.js
│   │   ├── BaseController.js
│   │   ├── Main.controller.js
│   │   ├── IncidentOverview.controller.js
│   │   ├── RootCauseAnalytics.controller.js
│   │   └── AIRecommendations.controller.js
│   ├── view/
│   │   ├── App.view.xml
│   │   ├── Main.view.xml
│   │   ├── IncidentOverview.view.xml
│   │   ├── RootCauseAnalytics.view.xml
│   │   └── AIRecommendations.view.xml
│   ├── css/
│   │   └── style.css
│   ├── i18n/
│   │   └── i18n.properties
│   └── model/
│       ├── formatter.js
│       └── models.js
```

### Troubleshooting

| Problem | Solution |
|---------|----------|
| `Failed to load sap/f/library.js` | Remove `sap.f` from `manifest.json` and `index.html` library list |
| `Failed to load sap/viz/library.js` | Remove `sap.viz` from `manifest.json` and `index.html` library list |
| Navigation not working | Check `manifest.json` routing configuration — routes, targets, and patterns must match |
| KPI cards not in a row | Verify `HorizontalLayout` or `FlexBox` is used with proper CSS for the KPI container |
| Filters not working | Ensure the controller binds filter events and applies `sap.ui.model.Filter` correctly |
| AI analysis not showing results | Check that the controller sets model data on the recommendation panel after button press |
| HAI proxy not responding | Restart with `hai proxy start` — ensure port 6655 is free |
| Cline not generating code | Verify API key and Base URL in Cline settings match the proxy output |
| App not starting | Run `npm install` first, then `npx ui5 serve -o index.html` |

### Next Steps
- [SAP UI5 SDK Documentation](https://ui5.sap.com/)
- [SAP Fiori Design Guidelines](https://experience.sap.com/fiori-design-web/)
- [SAP BTP Cloud Foundry Deployment Guide](https://developers.sap.com/tutorials/btp-cf-buildpacks.html)
- [SAP AI Core Documentation](https://help.sap.com/docs/sap-ai-core)
- [Cline Documentation](https://docs.cline.bot/)
<!-- /summary -->
