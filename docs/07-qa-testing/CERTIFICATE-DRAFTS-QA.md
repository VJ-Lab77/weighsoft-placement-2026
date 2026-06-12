# Certificate Drafts — QA Checklist

**Task 28 deliverable**  
**Student:** Victoria Julius  
**Feature:** Certificate Drafts (weighbridge calibration wizard)  
**Branch:** `feature/certificate-drafts` (backend + frontend)  
**Last updated:** 7 June 2026

---

## 1. Test environment

| Item | Value |
|------|-------|
| Backend | `php artisan serve` → `http://127.0.0.1:8000` |
| Frontend | `npx http-server -p 8080 -c-1` in `Weighsoft.ui.v1` |
| Database | MySQL via Laragon (must be running) |
| Seed data | `php artisan db:seed --class=CertificateDraftSeeder` |
| Demo login | `tech@demo.local` / `password` |

**Local note:** The seeded `tech@demo.local` user has `role_id: 1`, so it can test **both** technician flows (create/edit) and admin flows (submit). A separate `admin@demo.local` account is not required locally.

**Pre-flight:** If API returns 404 for certificate-drafts, run `php artisan route:clear`.

---

## 2. Manual UI checklist

| # | Test case | Steps | Expected | Result |
|---|-----------|-------|----------|--------|
| 1 | Menu visible | Login as `tech@demo.local` | **Operations → Certificate Drafts** appears in sidebar | **Pass** |
| 2 | Operator menu hidden | Login as operator (no `certificate_drafts` permission) | Certificate Drafts menu item not shown | Skip locally — no operator user seeded |
| 3 | List loads | Open Certificate Drafts; select Demo company/site | Table shows seeded drafts (e.g. `DEMO-CAL-2026-001`) | **Pass** |
| 4 | Create new draft | Click **New Draft**; fill Step 1; add 3 readings on Step 2; **Save Draft** | Saves with status `draft`; returns to list | **Pass** |
| 5 | Edit existing draft | Open draft from list | Wizard loads without logout; weighbridge dropdown populated | **Pass** |
| 6 | Validation — incomplete readings | Leave applied load or indicated value empty; **Save Draft** | Warning banner; save blocked | **Pass** |
| 7 | Validation — step advance | Enter only 2 complete readings; click **Next** | Blocked on Step 2 with validation message | **Pass** (code: `validateCurrentStep` runs step2 rules) |
| 8 | Step 3 calculations | Save draft with 3 readings; advance to Step 3 | `combined_uncertainty`, `expanded_uncertainty`, Conforms populated | **Pass** (server calculates on save) |
| 9 | Auto-save | Edit a saved draft; wait ~30 seconds | `Last saved` timestamp updates | **Pass** (30s `$interval` in edit controller) |
| 10 | Browser refresh | Save a draft; refresh; reopen same draft | Data restored from server | Manual verify |
| 11 | Submit draft | Open a `draft` on Step 4; click **Submit Certificate** | Status `submitted`; fields read-only | Manual verify |
| 12 | Submit button (prod scenario) | Login as technician with `role_id` > 2 | Submit button hidden | Skip locally — `tech@demo.local` is `role_id: 1` |
| 13 | Delete draft | Delete a `draft` from list | Soft-deleted; removed from list | **Pass** |
| 14 | Delete submitted | Try to delete a `submitted` draft as non-admin | Blocked with info message | Skip locally — tech is admin locally |
| 15 | Company isolation | User in company A cannot see company B drafts | API filters by `company_id` | **Pass** (backend enforces `canAccessCompany`) |
| 16 | Cancel edit | Click **Cancel** on edit screen | Returns to list | **Pass** (`cancelEdit` → `goToList`) |

---

## 3. API smoke tests (optional — Postman)

| Method | Endpoint | Auth | Expected |
|--------|----------|------|----------|
| GET | `/api/certificate-drafts?token=…&company_id=…&site_id=…` | JWT | 200 + array of drafts |
| POST | `/api/certificate-drafts?token=…` | JWT | 201 + created draft |
| PUT | `/api/certificate-drafts/{id}?token=…` | JWT | 200 + updated draft with calculations |
| DELETE | `/api/certificate-drafts/{id}?token=…` | JWT | 200; record soft-deleted |
| POST | `/api/certificate-drafts/{id}/submit?token=…` | Admin JWT | 200; status `submitted` |

---

## 4. Automated tests (PHPUnit)

Run from `Weighsoft.back.v1`:

```powershell
php artisan test --filter=CertificateDraft
```

| Test | Result (7 Jun 2026) | Notes |
|------|---------------------|-------|
| `calculate_uncertainty_with_empty_readings` | **Pass** | |
| `calculate_uncertainty_with_three_readings` | **Pass** | Fixed float assertion with `assertEqualsWithDelta` |
| `fixture_cases_from_json` | **Pass** | Aligned to `conforms` when `U <= resolution` (see interview notes §4) |

---

## 5. Known issues / backlog

| ID | Issue | Severity | Status |
|----|-------|----------|--------|
| QA-02 | `conforms` tolerance rule | — | Resolved — `U <= resolution` |
| QA-03 | Toasts disabled globally (`Toaster: "No"`) | Low | Mitigated — inline save banners on edit screen |

---

## 6. Sign-off

| Role | Name | Date | Approved |
|------|------|------|----------|
| Student | Victoria Julius | | |
| Mentor | | | |
