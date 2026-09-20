# G1 — One Counter for Every Government Task

**Smart India Hackathon 2026 · Team LOGIC OVERDRIVE (Team ID: 119671)**

> "One counter for every government task."

G1 is an India-first, single-window citizen services platform that turns a citizen's need — spoken or typed in their own language — into a tracked government action. Instead of navigating dozens of portals, unclear document lists, and repeat visits with no follow-up, a citizen gets one guided entry point that finds the right scheme, files the right complaint, and chases the outcome for them.

## Problem

India ranks 97th of 193 countries on the UN E-Government Development Index (2024). The services already exist online — the gap is guided, single-window access:

| Today | With G1 |
|---|---|
| Many portals | One entry point |
| Unclear documents | Guided next step |
| Repeat visits | Submit online |
| No follow-up | Live status + escalation |

## Features

G1 is organized into four "counters," each a self-contained module:

1. **Find Scheme** — Voice or text intent search that matches a citizen to eligible government schemes and lays out the next steps.
2. **Report Issue** — Report civic problems (potholes, streetlights, water, garbage, electricity) with photo + location, auto-routed to the right department.
3. **Grievance Tracker** — Timeline-based tracking of filed reports/applications until resolution; unresolved-past-deadline items auto-escalate to a senior authority.
4. **Document Helper** — Exact document checklists for common government tasks (Ration Card, Income Certificate, PAN, Aadhaar, etc.), with per-account save/checklist state.

Supporting capabilities baked into the prototype:
- **Voice assistant** with bilingual (English/Hindi + Hinglish) intent parsing — understands phrases like "sikayat karni hai" or "student scholarship" and routes to the right counter automatically, with city/state detection from ~50 major Indian cities.
- **Live UI translation** across a set of Indian languages, powered by a Supabase Edge Function proxy in front of Claude.
- **Google Sign-In (Gmail OAuth)** for sending filed reports and identifying the signed-in citizen.
- **Accessibility controls** (text size, contrast, motion) persisted per-user.
- **Account-linked persistence** — saved schemes, filed reports, and document checklists follow the citizen's account.

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | Vanilla HTML / CSS / JavaScript (single-page app) |
| Backend | Supabase (Auth + Postgres) |
| AI & Language | Claude API (via a Supabase Edge Function `ai-proxy`), browser-based voice input |
| Auth | Supabase Auth + Google Identity Services (Gmail OAuth for report submission) |
| Security | Supabase Row-Level Security, audit logs |

### Architecture

    Citizen (text / voice / photo, any Indian language)
            │
            ▼
       G1 Web UI  ──────────────►  AI Intent Layer (Claude API)
            │                             │
            ▼                             ▼
      Service Counters        Retrieval from verified service data
     (Scheme · Issue ·
      Tracker · Documents)
            │
            ▼
    Govt. Data & Departments (verified workflows via API/adaptor layer)

**Escalation engine:** Tracker watches open requests → deadline missed → senior authority alerted → citizen sees updated status. This is what makes G1 proactive rather than a static form.

## Current Status

This repo contains a working **front-end prototype** (`gi1.html`) built as a single self-contained file, demonstrating the full citizen journey end-to-end with live Supabase-backed accounts:

- All four counters are functional with real interaction flows (not static mockups).
- Reports get real ticket IDs in `GI-YYYY-NNNNN` format.
- Language switching calls a live translation proxy.
- Gmail OAuth flow is wired up for report submission.
- Government department integration is stubbed/mocked — real department APIs are the next step (see Roadmap).

## Getting Started

1. Clone this repo and open `gi1.html` directly in a browser, or serve it with any static file server:

       npx serve .

2. The app connects to a pre-configured Supabase project for auth/data. To point it at your own Supabase project instead, replace the `SUPABASE_URL` and `SUPABASE_ANON_KEY` constants near the top of the script section in `gi1.html`.
3. **AI translation proxy:** deploy the `ai-proxy` Supabase Edge Function with an `ANTHROPIC_API_KEY` secret set — this keeps the Claude API key server-side rather than in the browser.
4. **Gmail sign-in:** paste your own Google OAuth Client ID where prompted in the Report Issue panel (needed to send filed reports via the signer's Gmail account).

No build step is required — it's a single HTML file with everything inlined.

## Roadmap

| Phase | Description |
|---|---|
| 01 · Prototype | Find Scheme + Report Issue + Tracker + Document Helper (this repo) |
| 02 · India Pilot | Limited services, select state |
| 03 · Validate | Measure task completion time, document readiness, issue-resolution visibility, and user comprehension/satisfaction |
| 04 · Expand | Add more departments and services via the API/adaptor layer, without a core rebuild |

**Known challenges & mitigations:**
- *Service data changes* → versioned service catalogue with verified source links
- *AI gives wrong guidance* → answers grounded only in approved workflow data; source + next step always shown
- *Department integration* → one API/adaptor per department, starting with pilot workflows
- *Language & digital divide* → multilingual UI, voice input, simple guided flows
- *Citizen trust & privacy* → minimal data collection, authenticated login, audit logs, clear consent

## Research Basis

G1 was built after studying existing Indian e-governance systems:

| System | What it does | What G1 adds |
|---|---|---|
| [CPGRAMS](https://pgportal.gov.in) | Central public grievance filing and tracking | Scheme discovery, document checklist, and auto-escalation in one flow |
| [MyScheme](https://myscheme.gov.in) | Scheme discovery and eligibility | Issue reporting, request tracking, and escalation after discovery |
| CM Window (Haryana) | State grievance redressal and monitoring | Voice-first, multilingual guidance across schemes, documents, and grievances |
| [UMANG](https://umang.gov.in) | One app aggregating many government services | AI intent understanding and proactive follow-up on every request |

Reference: [UN E-Government Survey 2024](https://publicadministration.un.org)

## Team

**LOGIC OVERDRIVE** — Team ID 119671 — Smart India Hackathon 2026, Student Innovation category.

## License

This project is licensed under the [MIT License](LICENSE).
