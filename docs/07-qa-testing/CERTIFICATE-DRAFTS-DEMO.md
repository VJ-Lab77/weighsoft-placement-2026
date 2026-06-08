# Certificate Drafts — Demo Script

**Task 37 deliverable**  
**Student:** Victoria Julius  
**Duration:** ~10 minutes  
**Audience:** Mentor / placement supervisor  
**Last updated:** 7 June 2026

---

## 1. Before the demo

1. Start **MySQL** in Laragon.
2. Start backend: `php artisan serve` in `Weighsoft.back.v1`.
3. Start frontend: `npx http-server -p 8080 -c-1` in `Weighsoft.ui.v1`.
4. Ensure seed data exists:
   ```powershell
   php artisan db:seed --class=CertificateDraftSeeder
   ```
5. Open browser: `http://127.0.0.1:8080`
6. If routes were cached: `php artisan route:clear`.

**Login:** `tech@demo.local` / `password` (used for the entire demo — this user has admin rights locally via `role_id: 1`).

---

## 2. Demo flow

### Part A — Create and save a draft (~5 min)

| Step | Action | What to say |
|------|--------|-------------|
| 1 | Login as `tech@demo.local` / `password` | "Users with `certificate_drafts` permission see the new menu item under Operations." |
| 2 | Navigate to **Operations → Certificate Drafts** | "Same pattern as Pallets — company/site filter, then a list." |
| 3 | Select **Demo Calibration Co** and **Demo Site** if prompted | "Drafts are scoped to company and site." |
| 4 | Point out seed draft `DEMO-CAL-2026-001` | "Seeder creates demo company, weighbridge, user, and sample drafts." |
| 5 | Click **New Draft** | "Four-step wizard: Header → Readings → Calculations → Review." |
| 6 | Step 1 — fill weighbridge, date, technician, capacity, resolution | "Header fields map to the certificate draft record." |
| 7 | Click **Next** → Step 2 | "Three readings minimum; applied load and indicated value are required." |
| 8 | Leave a reading empty; click **Save Draft** | "Validation blocks save and lists missing fields." |
| 9 | Complete all 3 readings; click **Save Draft** | "Server runs uncertainty calculations; returns to list on success." |
| 10 | Re-open the saved draft | "Data loads from API without logging the user out." |

### Part B — Wizard steps and auto-save (~3 min)

| Step | Action | What to say |
|------|--------|-------------|
| 11 | Click **Next** through to Step 3 (Calculations) | "Uncertainty values are calculated server-side on save." |
| 12 | Show uncertainty summary table | "Combined and expanded uncertainty from `CertificateDraftService`." |
| 13 | Edit a field; wait ~30 seconds | "Auto-save runs every 30 seconds and updates Last saved." |
| 14 | Advance to Step 4 (Review) | "Summary of certificate number, status, technician, readings." |

### Part C — Submit (~2 min)

| Step | Action | What to say |
|------|--------|-------------|
| 15 | On Step 4, click **Submit Certificate** on a `draft` record | "Submit finalises the draft; status becomes submitted." |
| 16 | Show read-only fields after submit | "Submitted drafts cannot be edited." |
| 17 | Return to list | "Submitted drafts stay visible in the list." |

> **Production note:** Submit is restricted to users with `role_id` 1 or 2. Locally, `tech@demo.local` has `role_id: 1`, so one login covers the full demo.

---

## 3. Talking points (if asked)

| Topic | Answer |
|-------|--------|
| **Why AngularJS + Laravel?** | Existing Weighsoft stack; placement adapted curriculum tasks to this stack. |
| **Reference module** | Pallets — UI Router nested states, Restangular CRUD, company/site filtering. |
| **Permission** | `usertypes.certificate_drafts = "true"` (string, not boolean). |
| **Data storage** | MySQL `certificate_drafts` + `certificate_readings`; soft deletes. |
| **Out of scope** | PDF export, PouchDB offline sync, `in_review` workflow (pending mentor). |

---

## 4. Fallback if something breaks

| Problem | Quick fix |
|---------|-----------|
| Infinite loading on list | Start MySQL; check API URL in frontend config |
| 404 on certificate-drafts API | `php artisan route:clear` |
| Logout on edit | Check Laravel log; weighbridge API needs company+site |
| Save validation errors | All 3 readings need applied load and indicated value |
| Empty list | Re-run `CertificateDraftSeeder`; confirm company/site selected |

---

## 5. Demo checklist (day-of)

- [ ] MySQL running
- [ ] Backend and frontend servers running
- [ ] Seed data present
- [ ] `tech@demo.local` / `password` login works
- [ ] At least one `draft` and one `submitted` record in list
- [ ] QA checklist reviewed (`docs/07-qa-testing/CERTIFICATE-DRAFTS-QA.md`)
