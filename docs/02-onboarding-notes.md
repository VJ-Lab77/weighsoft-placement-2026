# Onboarding Notes — WIL Placement

**Task 2 deliverable**  
**Student:** Victoria Julius  
**Last updated:** June 2026

---

## Purpose

These notes orient you to the tools, repositories, and stack for this placement. The curriculum mentions React Native, Expo, and Android Studio — **this project uses AngularJS and Laravel instead**. The adaptation mapping is documented in `docs/00-project-plan.md` §3.

*Environment install steps (Laragon, Composer, etc.) are handled separately — not repeated here.*

---

## Repositories

| Repo | Purpose | Remote |
|------|---------|--------|
| **Placement docs** | WIL documentation, plans, logbook | `github.com/VJ-Lab77/weighsoft-placement-2026` |
| **Weighsoft backend** | Laravel API | Azure DevOps — `Weighsoft.back.v1` |
| **Weighsoft frontend** | AngularJS SPA | Azure DevOps — `Weighsoft.ui.v1` |

### Local folder layout

```
WIL- __Victoria_Julius/          ← placement repo (GitHub)
├── docs/                        ← your WIL documents
└── Weighsoft.v1/
    ├── Weighsoft.back.v1/       ← Laravel backend (own .git)
    └── Weighsoft.ui.v1/         ← AngularJS frontend (own .git)
```

### Git workflow for placement work

1. **Documentation changes** — commit and push from the root `WIL- __Victoria_Julius` folder to GitHub.
2. **Code changes** — commit inside `Weighsoft.back.v1` or `Weighsoft.ui.v1` (Azure DevOps workflow per mentor guidance).
3. Do not commit secrets (`.env`, tokens, credentials) to any repo.

---

## Tools you need

| Tool | Role in this project | Curriculum equivalent |
|------|----------------------|------------------------|
| **Git** | Version control for docs and code | Git (same) |
| **GitHub** | Placement documentation remote (`VJ-Lab77`) | Repo hosting (same) |
| **Laragon** | Local PHP, MySQL, Composer | Node/npm in curriculum |
| **PHP 8.3** | Runs Laravel backend | — |
| **Composer** | PHP dependency manager | npm |
| **MySQL** | Database | — |
| **Web browser** | Runs the AngularJS frontend | Expo/Android emulator |
| **VS Code / Cursor** | Code editor | Same |
| **Postman** (optional) | Test API endpoints | Same idea |

**Not used for this placement:** Expo, Android Studio, React Native, TanStack Query, PouchDB (runtime), Zod (runtime).

---

## Tech stack summary

### Backend (`Weighsoft.back.v1`)

| Item | Version / detail |
|------|------------------|
| Framework | Laravel 8.40 |
| Language | PHP 8.3 |
| Database | MySQL |
| Auth | JWT (`tymon/jwt-auth`) |
| API style | RESTful, `routes/api.php` |
| Base controller | `JwtAuthController` |

### Frontend (`Weighsoft.ui.v1`)

| Item | Version / detail |
|------|------------------|
| Framework | AngularJS 1.4.8 |
| Routing | UI Router (`routes.js`) |
| HTTP client | Restangular (not `$http`) |
| UI | Bootstrap 4.5.0, DataTables |
| Notifications | Toastr |
| Controller pattern | `controller as System` |

---

## First files to read

Read these before changing code:

| Order | File | Why |
|-------|------|-----|
| 1 | `docs/01-system-overview.md` | Plain-English system intro |
| 2 | `docs/00-architecture-plan.md` | How components connect |
| 3 | `docs/05-certificate-drafts-spec.md` | What you are building |
| 4 | `Weighsoft.ui.v1/app/js/routes.js` | How navigation works |
| 5 | `Weighsoft.ui.v1/app/js/controllers/pallet/` | Module pattern to copy |
| 6 | `Weighsoft.back.v1/app/Http/Controllers/PalletController.php` | API pattern to copy |
| 7 | `Weighsoft.back.v1/routes/api.php` | Route registration |

---

## Curriculum concept mapping

When a task mentions a modern mobile pattern, use the Weighsoft equivalent:

| Task says | You implement |
|-----------|---------------|
| React Hook Form + Zod | `ng-model` + validation objects in `app/js/validation/` |
| TanStack Query | Restangular + `$Functions` factory |
| FlatList | `ng-repeat` in Bootstrap/DataTables table |
| PouchDB schema | Design doc only; store data in MySQL |
| Repository class | AngularJS factory (e.g. `CertificateDraftRepository`) |
| Navigation route | UI Router state in `routes.js` |

---

## Key codebase rules

1. **Never use `$http`** for domain API calls — use Restangular.
2. **Always use** `$rootScope.Start()` before async work and `$rootScope.Loaded()` on success.
3. **Always use** `$rootScope.Error(response)` on failure.
4. **Controllers** use `var vm = this` and templates reference `System.`.
5. **Templates** use `appHelper.templatePath('feature/action')`.
6. **New API controllers** extend `JwtAuthController` and call `parent::__construct()`.
7. **Company scoping** — filter lists by the logged-in user's `company_id`.

---

## Running the app (reference only)

Once your environment is configured (Laragon + backend `.env` + frontend `env.js`):

| Service | Typical local URL |
|---------|-------------------|
| Laravel API | `http://localhost:8000/api` (or Laragon virtual host) |
| AngularJS UI | `http://localhost/weighsoft.ui` (or configured path) |
| Login | `/login` → JWT issued → sidebar menu built from permissions |

Confirm exact URLs with your mentor — they depend on Laragon configuration.

---

## Azure DevOps access

The Weighsoft source repos live on Azure DevOps (`wowscales/Development`). You need:

- Read access to clone/pull `Weighsoft.back.v1` and `Weighsoft.ui.v1`
- Write access if mentor expects commits pushed to Azure (confirm policy)

Your placement GitHub repo is separate — use it for WIL evidence (docs, reflections, logbook).

---

## Who to ask when stuck

| Topic | Ask |
|-------|-----|
| Calibration formulas, certificate fields | Mentor / site supervisor (see `docs/04-mentor-interview-notes.md`) |
| Azure DevOps access, deploy process | Mentor / dev team |
| Laravel or AngularJS patterns | Reference Pallets module + `.cursor/rules/` in each repo |
| WIL task evidence requirements | University supervisor |

---

## Checklist — am I oriented?

- [ ] I know which repo holds my docs (GitHub placement repo)
- [ ] I know which folders hold backend and frontend code
- [ ] I understand Company → Site → Workstation → Weighbridge hierarchy
- [ ] I know to use Restangular, not `$http`
- [ ] I have read the three planning docs in `docs/`
- [ ] I know the Pallets module is the template for Certificate Drafts
- [ ] Environment setup is complete (Laragon — separate guide)

---

## Related documents

| Document | Content |
|----------|---------|
| `docs/01-system-overview.md` | What Weighsoft does |
| `docs/03-user-journey-pallets.md` | How an existing feature works end-to-end |
| `docs/04-mentor-interview-notes.md` | Requirements from mentor |
| `Weighsoft.ui.v1/docs/00-FRONTEND-ARCHITECTURE.md` | Official frontend architecture |
| `Weighsoft.back.v1/docs/02-architecture/01-ARCHITECTURE.md` | Official backend architecture |
