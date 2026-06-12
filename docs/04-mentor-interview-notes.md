# Mentor Interview Notes — Certificate Drafts Requirements

**Task 4 deliverable**  
**Student:** Victoria Julius  
**Interview status:** Mentor feedback not received — requirements **confirmed by student** (June 2026) using Weighsoft patterns, implemented MVP, and long-term maintainability. Revisit with mentor when available.  
**Last updated:** 7 June 2026

---

## 1. Interview purpose

Capture real-world requirements for the **Certificate Drafts** feature. Original plan was a live mentor interview; in its absence, decisions below align the spec and code for production use and future mentor review.

---

## 2. Interview questions (reference)

_Questions in §2 remain as a checklist if a mentor review happens later._

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
| Date | Not held — requirements confirmed from implementation review |
| Mentor name | _Pending — revisit when mentor available_ |
| Role | — |
| Location | — |
| Duration | — |

---

## 4. Confirmed requirements

> **Source:** Student confirmation (no mentor feedback). Matches implemented MVP and `docs/05-certificate-drafts-spec.md`. Mentor may revise later.

### Workflow

| Topic | Answer | Status |
|-------|--------|--------|
| Approval before submit | **No mandatory `in_review` for MVP.** Technician saves `draft`; admin submits to `submitted`. Status `in_review` reserved for a future approval step. | CONFIRMED |
| Submitted drafts editable | **No** — read-only after submit (audit trail) | CONFIRMED |
| Calibration frequency | **At least annually** per weighbridge (typical industry practice) | CONFIRMED |
| Who performs calibration | **Internal technician** primary; external contractor data entered manually (no separate contractor portal) | CONFIRMED |
| Current paperwork | **Paper / spreadsheet today** — Certificate Drafts digitises the record inside Weighsoft | CONFIRMED |

### Certificate fields (minimum viable)

| Field | Required? | Status |
|-------|-----------|--------|
| Certificate number | Yes — auto-generated `{prefix}-CAL-{YYYY}-{seq}` | CONFIRMED |
| Weighbridge | Yes — dropdown from site's weighbridges | CONFIRMED |
| Calibration date | Yes | CONFIRMED |
| Technician name | Yes — default to logged-in user | CONFIRMED |
| Capacity | Yes — manual entry on wizard (from weighbridge context) | CONFIRMED |
| Resolution (kg/division) | Yes | CONFIRMED |
| Coverage factor (k) | Yes — default **2** | CONFIRMED |
| Client company on certificate | **Company name from record** — full letterhead/PDF export deferred to future phase | CONFIRMED |

### Readings

| Topic | Answer | Status |
|-------|--------|--------|
| Minimum readings | **3** | CONFIRMED |
| Maximum readings | **20** | CONFIRMED |
| Load points | **Suggested** 20%, 50%, 100% of capacity — guidance only, not enforced (technician flexibility) | CONFIRMED |
| Repeatability values | **One per reading row** | CONFIRMED |
| Units | **kg, t, lbs** | CONFIRMED |
| Failure tolerance | **`conforms = true` when `U <= resolution`** (expanded uncertainty within one scale division) | CONFIRMED |

### Uncertainty formulas

| Symbol | Formula | Status |
|--------|---------|--------|
| Error | `e = indicated_value − applied_load` | CONFIRMED |
| Mean error | `ē = mean(e_i)` | CONFIRMED |
| Repeatability uncertainty | `u_rep = stdDev(repeatability_values) / √n` (sample std dev, n−1) | CONFIRMED |
| Resolution uncertainty | `u_res = resolution / (2√3)` (rectangular distribution) | CONFIRMED |
| Combined uncertainty | `u_c = √(u_rep² + u_res²)` | CONFIRMED |
| Expanded uncertainty | `U = k × u_c` (k = 2) | CONFIRMED |
| Conforms | `U <= resolution` | CONFIRMED |
| Standard reference | **GUM-style in-house implementation** — not certified against OIML/SANS until mentor/site review | CONFIRMED |

**Worked example (seed fixture):** resolution 20, k = 2, three readings → `u_c ≈ 5.89`, `U ≈ 11.78`, `conforms = true`. See `tests/fixtures/certificateCalculations.json`.

### Roles

| Action | Who | Status |
|--------|-----|--------|
| Create / edit drafts | Users with `certificate_drafts = "true"` | CONFIRMED |
| Submit drafts | Admin roles (`role_id` 1 or 2) | CONFIRMED |
| Delete `draft` records | Users with certificate_drafts permission | CONFIRMED |
| Delete `submitted` records | Admin only | CONFIRMED |
| Company data isolation | All API queries filtered by `company_id` | CONFIRMED |
| Site filtering | UI shell selects company/site (same pattern as Pallets); list scoped to selected site | CONFIRMED |
| Default permission | **Calibration Technician** usertype gets `certificate_drafts = true` in seeder; admins (role 1–2) can submit | CONFIRMED |

### Company admin

| Improvement | Priority | Status |
|-------------|----------|--------|
| Certificate prefix per company | **Should** — Task 6 | CONFIRMED |
| Pending drafts dashboard card | **Could** — Task 6 | CONFIRMED |
| Clearer user membership on user list | **Could** — Task 6 | CONFIRMED |

---

## 5. Decisions made

| # | Decision | Rationale | Date |
|---|----------|-----------|------|
| 1 | Build as web module (AngularJS), not mobile | Matches existing Weighsoft stack | 2026-06 |
| 2 | Store in MySQL now, not PouchDB | No offline infra; aligns with all other modules | 2026-06 |
| 3 | Use 4-step wizard | Separates header, readings, calculations, review clearly | 2026-06 |
| 4 | Auto-save every 30 seconds | Prevents data loss during long calibration sessions | 2026-06 |
| 5 | Copy Pallets module structure | Proven pattern in codebase | 2026-06 |
| 6 | Skip mandatory `in_review` for MVP | Reduces workflow complexity; admin submit is sufficient for placement | 2026-06 |
| 7 | Conforms when `U <= resolution` | Achievable with real readings; `0.5×resolution` fails even with ideal resolution-only uncertainty | 2026-06 |
| 8 | Load points as guidance, not validation | Sites use different calibration procedures; hard rules would block valid work | 2026-06 |
| 9 | PDF export out of scope | Matches Weighsoft phased delivery; drafts stored in DB first | 2026-06 |

---

## 6. Out of scope (confirmed)

| Item | Reason |
|------|--------|
| PDF certificate export | Future phase after MVP stabilises |
| Digital signatures | Not in current Weighsoft patterns |
| Offline / PouchDB sync | Documented for future only |
| External calibration lab portal | Manual data entry for placement |
| Mandatory manager `in_review` step | Deferred — admin submit sufficient for MVP |

---

## 7. Action items

- [x] Replace `PENDING` / `ASSUMED` entries in §4 with confirmed answers
- [x] Update `docs/05-certificate-drafts-spec.md` §8 (formulas) and §19 (close open questions)
- [x] Align test fixtures with confirmed formulas
- [ ] Mentor review when available — may adjust tolerance rule or formulas

---

## Related documents

| Document | Content |
|----------|---------|
| `docs/05-certificate-drafts-spec.md` | Feature spec — updated to match §4 |
| `docs/06-company-admin-improvement.md` | Related admin improvements (Task 6) |
| `docs/00-project-plan.md` | Phase A exit criteria |
