# Mentor Interview Notes — Certificate Drafts Requirements

**Task 4 deliverable**  
**Student:** Victoria Julius  
**Interview status:** Template prepared — **confirm answers with mentor and update §4**  
**Last updated:** June 2026

---

## 1. Interview purpose

Capture real-world requirements for the **Certificate Drafts** feature from a mentor or site supervisor before implementation. Answers here will update `docs/05-certificate-drafts-spec.md` §19 (open questions).

---

## 2. Interview questions

### A — Calibration workflow

| # | Question |
|---|----------|
| A1 | How often are weighbridges calibrated at your sites? |
| A2 | Who performs calibration — internal technician or external contractor? |
| A3 | What paperwork is produced today (paper certificate, PDF, spreadsheet)? |
| A4 | Does a draft need manager approval before it becomes a final certificate? |
| A5 | Should submitted drafts be editable, or locked permanently? |

### B — Certificate fields

| # | Question |
|---|----------|
| B1 | What fields must appear on a calibration certificate? |
| B2 | Is a certificate number assigned manually or auto-generated? What format? |
| B3 | Which weighbridge details must be captured (name, code, capacity, resolution)? |
| B4 | Is technician name / sign-off required? |
| B5 | Are client company details printed on the certificate? |

### C — Test readings

| # | Question |
|---|----------|
| C1 | How many test readings are required per calibration? |
| C2 | What load points are used (e.g. 0%, 50%, 100% of capacity)? |
| C3 | Is repeatability tested separately? How many repeat runs? |
| C4 | What units are used (kg, tonnes)? |
| C5 | What is the acceptable error tolerance before a bridge fails? |

### D — Uncertainty calculations

| # | Question |
|---|----------|
| D1 | Which uncertainty formula or standard do you follow (OIML, SANS, in-house)? |
| D2 | What is the coverage factor (k) — typically 2? |
| D3 | How is repeatability uncertainty calculated from test data? |
| D4 | How is resolution uncertainty included? |
| D5 | Can you provide a worked example with known inputs and expected outputs? |

### E — Roles and access

| # | Question |
|---|----------|
| E1 | Which user types should create drafts? |
| E2 | Which user types can submit/finalise certificates? |
| E3 | Should users only see drafts for their own company? |
| E4 | Should site-level filtering apply (user sees only their site)? |

### F — Company admin

| # | Question |
|---|----------|
| F1 | Should each company have its own certificate number prefix? |
| F2 | Would a dashboard count of pending drafts be useful? |
| F3 | Any improvements needed to the existing Company admin screen? |

---

## 3. Interview log

| Field | Value |
|-------|-------|
| Date | _[To be completed]_ |
| Mentor name | _[To be completed]_ |
| Role | _[To be completed]_ |
| Location | _[To be completed]_ |
| Duration | _[To be completed]_ |

---

## 4. Confirmed requirements

> **Instructions:** After the interview, replace `PENDING` entries with confirmed answers. Items marked `ASSUMED` are working defaults until confirmed.

### Workflow

| Topic | Answer | Status |
|-------|--------|--------|
| Approval before submit | Manager review optional — status `in_review` before `submitted` | ASSUMED |
| Submitted drafts editable | No — read-only after submit | ASSUMED |
| Calibration frequency | At least annually per weighbridge | PENDING |
| Current paperwork | Paper / spreadsheet — no system record today | ASSUMED |

### Certificate fields (minimum viable)

| Field | Required? | Status |
|-------|-----------|--------|
| Certificate number | Yes — auto-generated `{company_code}-CAL-{YYYY}-{seq}` | ASSUMED |
| Weighbridge | Yes — dropdown from site's weighbridges | ASSUMED |
| Calibration date | Yes | ASSUMED |
| Technician name | Yes — default to logged-in user | ASSUMED |
| Capacity | Yes — from weighbridge or manual entry | ASSUMED |
| Resolution (kg/division) | Yes | ASSUMED |
| Coverage factor (k) | Yes — default 2 | ASSUMED |

### Readings

| Topic | Answer | Status |
|-------|--------|--------|
| Minimum readings | 3 | ASSUMED |
| Maximum readings | 20 | ASSUMED |
| Load points | Evenly spaced: 20%, 50%, 100% of capacity suggested | PENDING |
| Repeatability values | One per reading row | ASSUMED |
| Failure tolerance | `U > 0.5 × resolution` → does not conform | PENDING |

### Uncertainty formulas (working defaults — confirm with mentor)

| Symbol | Formula | Status |
|--------|---------|--------|
| Error | `e = indicated_value − applied_load` | ASSUMED |
| Mean error | `ē = mean(e_i)` | ASSUMED |
| Repeatability uncertainty | `u_rep = stdDev(repeatability_values) / √n` | ASSUMED |
| Resolution uncertainty | `u_res = resolution / (2√3)` | ASSUMED |
| Combined uncertainty | `u_c = √(u_rep² + u_res²)` | ASSUMED |
| Expanded uncertainty | `U = k × u_c` (k = 2) | ASSUMED |
| Conforms | `U` within tolerance (TBD with mentor) | PENDING |

**Action after interview:** Update `docs/05-certificate-drafts-spec.md` §8.1 and test fixtures with confirmed formulas.

### Roles

| Action | Who | Status |
|--------|-----|--------|
| Create / edit drafts | Users with `certificate_drafts = true` | ASSUMED |
| Submit drafts | Admin roles (`role_id` 1 or 2) | ASSUMED |
| Delete drafts | Admin only | ASSUMED |
| Company data isolation | All queries filtered by `company_id` | ASSUMED |

### Company admin (from interview section F)

| Improvement | Priority | Status |
|-------------|----------|--------|
| Certificate prefix per company | Should | ASSUMED — see `docs/06-company-admin-improvement.md` |
| Pending drafts dashboard card | Could | ASSUMED |
| Clearer user membership display on user list | Could | ASSUMED |

---

## 5. Decisions made

| # | Decision | Rationale | Date |
|---|----------|-----------|------|
| 1 | Build as web module (AngularJS), not mobile | Matches existing Weighsoft stack | 2026-06 |
| 2 | Store in MySQL now, not PouchDB | No offline infra exists; aligns with all other modules | 2026-06 |
| 3 | Use 4-step wizard | Separates header, readings, calculations, review clearly | 2026-06 |
| 4 | Auto-save every 30 seconds | Prevents data loss during long calibration sessions | 2026-06 |
| 5 | Copy Pallets module structure | Proven pattern in codebase | 2026-06 |

_Add mentor-confirmed decisions here after interview._

---

## 6. Out of scope (confirmed or assumed)

| Item | Reason |
|------|--------|
| PDF certificate export | Future phase — not needed for MVP |
| Digital signatures | Not in current Weighsoft patterns |
| Offline / PouchDB sync | Documented for future only |
| Integration with external calibration labs | Manual data entry for placement |

---

## 7. Action items after interview

- [ ] Replace all `PENDING` and `ASSUMED` entries in §4 with confirmed answers
- [ ] Update `docs/05-certificate-drafts-spec.md` §8 (formulas) and §19 (close open questions)
- [ ] Update test fixtures in spec §8.3 with mentor's worked example
- [ ] Sign off Phase A with mentor (exit criteria in project plan)

---

## Related documents

| Document | Content |
|----------|---------|
| `docs/05-certificate-drafts-spec.md` | Feature spec to update after interview |
| `docs/06-company-admin-improvement.md` | Related admin improvements |
| `docs/00-project-plan.md` | Phase A exit criteria |
