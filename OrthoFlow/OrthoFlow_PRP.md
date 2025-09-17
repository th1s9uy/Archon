# Project Requirements Plan (PRP)

**Working Title:** OrthoFlow — Orthopedic EHR + Billing

**Sponsor / Stakeholder:** \[Orthopedic Specialist Name], Owner/Provider
**Prepared by:** Sean Nicholas (DataNinja, Inc.)
**Date:** Sept 3, 2025
**Status:** Draft v1.0

---

## 1) Problem & Vision

**Problem.** Current system doesn’t fully meet an orthopedic physician practice’s needs for integrated clinical documentation, claims/RBM, and inventory/implant tracking in one workflow. Manual steps across intake → charting → ordering DME/implants → coding → claims create friction, denials, and rework.

**Vision.** A focused, cloud-native EHR + RCM tailored to orthopedics that streamlines end-to-end encounters, integrates coding/claims and inventory, and provides clear patient progress tracking with built-in analytics.

**Top Outcomes**

* Reduce provider documentation time by ≥40% while increasing note quality and compliance.
* Clean-claim 1st-pass rate ≥ 95%, DSO < 28 days.
* End-to-end traceability of items (implants/DME) with lot/UDI tracking and patient linkage.
* Patient progress views (ROM, pain scores, imaging, PT milestones) surfaced at point of care.

---

## 2) Users & Personas

* **Orthopedic Surgeon/PA/NP** — creates notes, orders, e-prescribes, reviews imaging, performs procedures.
* **Front Desk/Scheduler** — intake, eligibility checks, consents, forms, copays, referrals.
* **Billing Specialist/RCM** — charge entry, edits/scrubbing, submissions, ERAs, denials.
* **Inventory/OR Tech** — check-in/out items, track lot/serial/UDI, reconcile to charges.
* **Patient** — portal, forms, payments, care plan, messages.

---

## 3) Goals / Non‑Goals

**Goals**

1. Seamless encounter workflow: intake → SOAP/procedure note → codes → claim → remittance.
2. Built-in orthopedic content: templates, macros, ordersets, common CPT/HCPCS + modifiers (e.g., 25, 59, 50, LT/RT).
3. RCM automation: eligibility 270/271, claim 837P, remittance 835, status 276/277, prior auth 278.
4. Inventory & implant/DME link to encounters and claims; rental/stock-and-bill support.
5. Patient progress dashboards and outcomes tracking.
6. Interop: eRx, eFax, FHIR/HL7 for labs/imaging/scheduling; export of EHI.

**Non‑Goals (MVP)**

* Inpatient (837I) workflows; full ONC certification on Day 1; advanced research modules; PT/OT full EHR (document basic PT notes only); full payer contract modeling.

---

## 4) Scope — MVP Capabilities

1. **Clinical Notes & Orders**

   * SOAP/procedure notes with ortho templates (knee/shoulder/hip/spine/hand), smart phrases, dictation and optional voice capture.
   * Procedure logging (CPT/HCPCS), charges auto-suggested from note, NCCI edits and modifier prompts.
   * E‑signature, consents, attestation; PDF export.

2. **Scheduling, Intake & Patient Portal**

   * Multi-location, provider schedules, waitlist, slot types.
   * Digital intake (demographics, insurance cards, ROS, PROMs), e-consent, photo capture.
   * Patient portal: forms, results, messages, payments, visit summaries.

3. **Billing & RCM**

   * Payer/plan master; patient insurance; eligibility (270/271).
   * Claim creation (837P), claim edits/scrubbing, EDI to clearinghouse; claim status (276/277).
   * Remittance (835) auto-posting with payer rules; denial worklist; statements and payment plans.
   * Workers’ comp support (basic); ref/auth tracking; superbills; ICD-10 mapping.

4. **Inventory & Item Tracking**

   * Catalog (implants/DME), barcode/UDI, lot & serial tracking, min/max counts.
   * Check-in/out, stock-and-bill and rental, tie to encounter & charge; item usage reconciliation.

5. **Reporting & Progress Tracking**

   * Dashboard: charges, denials, DAR, no-show %, provider productivity.
   * Patient progress: outcomes/PROMs; ROM, pain scores; imaging milestones; PT goals.

6. **Administration & Security**

   * Roles/permissions (RBAC), audit logs, break-glass; org/locations/providers.
   * Templates library; codes & fee schedules; payer-specific rules; document library.

---

## 5) Regulatory & Compliance (MVP posture)

* HIPAA privacy/security; BAAs with vendors.
* Encryption in transit+at rest; access logging; minimum necessary; PHI data retention policy.
* EDI HIPAA transactions: 270/271, 276/277, 837P, 835.
* FHIR R4/R5 read APIs for core resources (Patient, Practitioner, Encounter, Observation, DocumentReference) to enable data portability; EHI export.
* Information Blocking posture (read-only APIs + export) while full certification is deferred to Phase 2.

---

## 6) End-to-End Workflows

**A. New Patient Visit**

1. Schedule → 2) Eligibility check → 3) Patient completes intake (portal/iPad) → 4) Rooming vitals + PROMs → 5) Provider notes (template+dictation) → 6) Codes auto-suggested; edits/validation → 7) E‑sign → 8) Claim generated → 9) Submit to clearinghouse → 10) ERA posts → 11) Patient balance billed if needed.

**B. Procedure with Implant/DME (Stock & Bill)**

1. Provider orders item from catalog → 2) Inventory picks item (scan UDI/lot); consumption logged → 3) Charge links to item and lot → 4) Claim includes HCPCS/CPT with modifiers; 5) Rental or purchase logic applied → 6) Track follow-ups and outcomes.

**C. Denial Management**
ERA load → auto-post → denials routed to worklists (CARC/RARC) → tasking → resubmission.

---

## 7) Data Model (initial)

**Core Entities**

* Person: Patient, Guarantor, EmergencyContact
* Clinical: Encounter, Note, Procedure, Diagnosis, Observation, ImagingStudyRef, Document
* Inventory: Item, Catalog, Lot/Serial, UDI, LocationBin, Transaction (in/out/consume), Rental
* Billing: Payer, Plan, Provider, FeeSchedule, Charge, Claim, ClaimLine, Remit(ERA), Payment, Adjustment, Auth/Referral
* Ops: Appointment, Task, Message, Attachment, AuditLog, Org, Location, User, Role

**Key Relations**
Patient 1‑\* Encounter; Encounter 1‑\* Note, Charge; Charge \*‑1 FeeScheduleCode; Charge *‑1 Item/Lot (optional); Claim 1‑* ClaimLine *‑1 Charge; ERA 1‑* RemitLine → ClaimLine.

---

## 8) Integrations (MVP choices)

* **Clearinghouse:** \[Availity/Optum/Waystar; choose 1] — 270/271, 276/277, 837P, 835.
* **eRx:** Surescripts-certified partner (Phase 2 for full certification); e-fax (Twilio/InterFAX) for scripts/referrals in MVP.
* **Payments:** Stripe/Adyen; card-on-file, HSA/FSA.
* **PACS/Imaging:** Vendor-neutral links; DICOM viewer link-outs; Phase 2 for HL7/FHIR imaging.
* **PROMs/Surveys:** In‑app forms (MVP); optional 3rd‑party later.

---

## 9) Architecture (proposed)

* **Frontend:** React/Next.js (TypeScript), component library with accessible patterns, offline form buffering.
* **Backend:** Python FastAPI; SQLModel/Pydantic v2; Celery + Redis/RabbitMQ for EDI jobs; workers isolated by tenant.
* **DB:** PostgreSQL (OLTP) with row-level security per tenant; Timescale (optional) for vitals/observations.
* **Storage:** Object store (Azure Blob/S3) for docs/scans; pre-signed URLs.
* **Interop:** FHIR read API (Patient, Practitioner, Encounter, Observation, DocumentReference, Procedure, Claim); webhooks.
* **EDI:** X12 service for 270/271, 276/277, 837P, 835 (parser/generator; clearinghouse SFTP/API).
* **Security:** RBAC, SSO (OIDC), audit trail, field-level masks, PHI redaction in logs.
* **DevOps:** IaC (Terraform), CI/CD, blue‑green deploys, per-tenant feature flags; observability (OpenTelemetry).

---

## 10) Success Metrics (MVP)

* 1st‑pass clean claim rate ≥95%; Denial rate <5%; Eligibility failure denials reduced by ≥60%.
* Average note completion time < 6 minutes for common visits.
* ERA auto-posting coverage ≥85% of payers; <5% manual posting.
* Inventory mismatch shrinkage <1% of monthly item value.

---

## 11) Risks & Mitigations

* **Certification timeline** (ONC, eRx): stage features as read‑only APIs now; plan Phase 2 certification track.
* **Clearinghouse dependency:** dual adapters; abstract EDI; sandbox testing before go‑live.
* **Data migration:** design importers for demographics, insurance, balances, item masters; CSV/JSON + manual assist.
* **Change management:** embedded templates, quick actions, provider hotkeys; training materials.

---

## 12) Milestones & Deliverables (indicative)

**M0 – Foundation (2–3 wks)**
Env, auth, org/loc/users, RBAC, audit, core schema, CI/CD, seed data.

**M1 – Intake & Scheduling (3–4 wks)**
Calendar, portal forms, eligibility 270/271, document mgmt, e-consent.

**M2 – Notes & Coding (4–5 wks)**
Templates, voice/dictation, charge capture, NCCI edits, export PDFs.

**M3 – Claims & ERA (4–5 wks)**
837P generation + submission adapter; 276/277, 835 posting, denial worklists.

**M4 – Inventory & DME (3–4 wks)**
Catalog, UDI/lot tracking, stock-and-bill/rentals, barcode.

**M5 – Reporting & Portal (2–3 wks)**
Dashboards, patient progress, payments, statements.

Pilot launch → 1 practice, 2 providers, 1 location.

---

## 13) Feature Backlog (developer-ready outline)

* [ ] **Schema**: Patients, Payers/Plans, Providers, Appointments, Encounters, Notes, Diagnoses, Procedures, Charges, Claims, ERA, Payments, Items, Lots, InventoryTxns, Tasks, Messages, AuditLogs, Orgs/Locations/Users/Roles.
* [ ] **RBAC & Audit**: role matrix; event trail for PHI object access.
* [ ] **Eligibility 270/271**: build request/response mappers; payer sandbox tests.
* [ ] **Claim 837P**: generator; claim edit rules; submit adapter (SFTP/API).
* [ ] **ERA 835**: parser; auto-posting; CARC/RARC mapping; worklist.
* [ ] **Claim Status 276/277**: polling + reconciliation to AR.
* [ ] **Notes**: ortho templates (knee/shoulder/hip/spine/hand); dictation integration; macro engine.
* [ ] **Coding assistance**: CPT/HCPCS suggestion from note; modifier helper; NCCI checker.
* [ ] **Inventory**: item master, UDI/lot scan, consume-in-encounter, rental logic, reconciliation.
* [ ] **Portal**: forms, e-consent, statements, payments, messages.
* [ ] **Reports**: claims funnel, denial heatmap, productivity, inventory shrinkage.
* [ ] **FHIR Read API**: Patient, Practitioner, Encounter, Observation, DocumentReference, Procedure, Claim.
* [ ] **Ops**: tasks/notifications, templates admin, fee schedule import.

**Acceptance Criteria examples**

* *837P*: Given a coded encounter with valid patient/insurance/provider, when I generate and submit a claim, then an X12 837P file passes clearinghouse validation, and a claim status record is created.
* *835*: Given an ERA file for a submitted claim, when I import it, then payments/adjustments post to the claim ledger and any denials generate tasks with CARC/RARC codes.

---

## 14) UX Guidance (high level)

* One-screen encounter view: note on left, codes/charges on right, inventory actions inline.
* Minimal-click coding; keyboard-first; clear modifier prompts.
* Patient progress timeline with filters (procedure date, PROMs, imaging).
* Color‑safe, accessible components; offline-safe form buffers.

---

## 15) Environment & Tenancy

* Multi-tenant (org\_id scoping, RLS), per-tenant S3/Blob prefixes, separate EDI endpoints.
* Audit vault (WORM-capable), export tooling per tenant.

---

## 16) Open Questions

* Which clearinghouse will the practice use? (Availity/Waystar/Optum/etc.)
* eRx timing & partner? (Phase 1: eFax only vs full eRx)
* Imaging vendors; PACS linkout needs.
* Existing data export availability from the current system (demographics, balances, inventory).
* Workers’ comp & PI requirements (forms, billing rules).

---

## 17) Appendix — Glossary (for devs)

* **X12 270/271** eligibility, **276/277** status, **837P** professional claims, **835** remittance.
* **NCCI** edits, **CARC/RARC** denial codes.
* **FHIR** core resources; **USCDI** data classes (Phase 2).
* **UDI**: Unique Device Identifier for implants/DME; lot/serial tracking.
