# APC Integrated Business Systems (APCHub)

[![PHP](https://img.shields.io/badge/PHP-8.3+-%23777BB4?logo=php&logoColor=white)](https://www.php.net)
[![Laravel](https://img.shields.io/badge/Laravel-13.x-%23FF2D20?logo=laravel&logoColor=white)](https://laravel.com)
[![Livewire](https://img.shields.io/badge/Livewire-4-%23fb70a9)](https://livewire.laravel.com)
[![Tailwind CSS](https://img.shields.io/badge/Tailwind_CSS-3-%2306B6D4?logo=tailwindcss&logoColor=white)](https://tailwindcss.com)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-%234169E1?logo=postgresql&logoColor=white)](https://www.postgresql.org)
[![Python](https://img.shields.io/badge/Python-3.13-%233776AB?logo=python&logoColor=white)](https://www.python.org)
[![Docker](https://img.shields.io/badge/Docker-%232496ED?logo=docker&logoColor=white)](https://www.docker.com)

**An enterprise platform that unifies multi-branch financial operations, HR and biometric
timekeeping, field marketing, and IT infrastructure management into three interlocking systems.**

> **About this repository** — This is a *public case-study mirror* of a private, production
> codebase. Source code, credentials, and deployment specifics remain private; this document
> presents the product and the engineering behind it. Interested in a closer look? Open an
> issue or request a demo and we can walk through a live environment.

---

## Table of Contents

- [The Challenge](#the-challenge)
- [The Solution — Three Interlocking Systems](#the-solution--three-interlocking-systems)
- [What This Project Demonstrates](#what-this-project-demonstrates)
- [Feature Highlights](#feature-highlights)
- [Technical Architecture](#technical-architecture)
- [Edge & Automation Engineering](#edge--automation-engineering)
- [Tech Stack](#tech-stack)
- [Developer Setup](#developer-setup)
- [Repository Layout](#repository-layout)

---

## The Challenge

A multi-branch financial institution operated on disconnected islands: a legacy pension
database, paper-based field operations, independently configured fingerprint time-attendance
machines at every branch, and no shared view across corporate, HR, marketing, and IT.

The organization needed a single platform that could:

- **Centralize operations** — loans, passbook/ATM releases, pensions, and administrative
  workflows in one audited system with role-based access per branch.
- **Automate timekeeping** — manage dozens of ZKTeco/NGTeco fingerprint devices across
  branch offices without sending technicians for every config change.
- **Connect the field** — enable marketing and field crews to log inquiries, campaigns, and
  saturation visits from a phone, tied to a shared Branch → Office → Municipality →
  Barangay → Purok geography.
- **Modernize the back office** — migrate 20+ years of legacy pension data and keep daily
  reconciliation auditable.

---

## The Solution — Three Interlocking Systems

| # | System | What it is | Built with |
|---|---|---|---|
| 1 | **Web Application** | A TALL-stack ERP powering all corporate, branch, HR, marketing, and infrastructure workflows, with real-time reactive interfaces. | Laravel 13 · Livewire 4 · Alpine.js · Tailwind 3 · PostgreSQL |
| 2 | **Desktop Hardware Tools** | Compiled Windows GUI + headless agent binaries that manage biometric hardware at every branch and sync it to the web backend. | Python 3.13 · PyInstaller · customtkinter · pyzk |
| 3 | **Reconciliation & Automation** | Legacy data migration commands, CSV import pipelines, ledger auditors, scheduled jobs, and a cloud branch-sync server with off-site backups. | Laravel artisan · Flask · AWS S3 |

```
┌─────────────────────────────────────────────────────────────────────┐
│                    BRANCH OFFICES (Windows PCs)                      │
│                                                                     │
│  ┌─────────────────────┐   ┌──────────────────────────────────────┐ │
│  │  ZKTeco / NGTeco    │──▶│  Desktop Tools (compiled .exe)       │ │
│  │  fingerprint units  │   │  headless agent (daemon)             │ │
│  │  (LAN / proprietary │◀──│  management console (GUI)            │ │
│  │   TCP protocol)     │   │  offline device manager              │ │
│  └─────────────────────┘   └───────────────┬──────────────────────┘ │
└────────────────────────────────────────────┼────────────────────────┘
                                             │ HTTPS (API key / token)
                                             │ WebSocket (real-time)
                 ┌───────────────────────────▼───────────────────────────┐
                 │           Web Backend (Laravel 13)                    │
                 │  Custom RBAC · Livewire 4 · Realtime · PostgreSQL     │
                 │  Queues · Broadcasting · Audited activity log         │
                 └───────────────┬───────────────────┬───────────────────┘
                                 │                   │
                    HTTP (API key)          DB connections
                 ┌───────────────▼───────┐   ┌────────▼────────┐
                 │ branch sync service   │   │ legacy pension  │
                 │ (Flask · delta sync · │   │ database (legacy,│
                 │  off-site backups)    │   │  + modern ILV db)│
                 └───────────────────────┘   └─────────────────┘
```

---

## What This Project Demonstrates

A quick map of **feature → skill**, useful for evaluating fit:

| You care about… | See in this project |
|---|---|
| **Full-stack TALL development** | Monolithic Laravel app with 88+ Livewire components across 20 modules, 100+ Eloquent models, 254 Blade views. |
| **Real-time engineering** | WebSocket broadcasting for chat (presence, read receipts, reactions), live biometric device telemetry, and reactive UI — with queue workers and background polling fallbacks. |
| **Authorization design** | A custom RBAC system (roles + direct permissions, manager-subordinate hierarchies, per-button visibility) — not a package drop-in. |
| **Security hardening** | Custom 2FA, session fingerprinting, forced password rotation, soft deletes, security headers, per-route middleware contracts, and a global audit log. |
| **Mobile-first UI** | The largest workflows (asset management, marketing field ops) are designed and built for 320px phones first, then progressively enhanced. |
| **Hardware + embedded integration** | Biometric devices speaking a proprietary TCP protocol, bridged through compiled Windows agents with write-verify loops and self-update. |
| **Legacy migration** | Moving tens of thousands of rows from a pre-2000s legacy DB into a modern schema, with per-row fault isolation and reconciliation audits. |
| **Automation & operations** | Scheduled jobs (billing generation, reminders, device health), CSV import pipelines with heuristic column repair, and delta-sync with off-site backup. |

**Scale today**: 208 migrations · ~105 Eloquent models · ~88 Livewire components ·
76 controllers · 21 artisan commands · 14 broadcast events.

---

## Feature Highlights

### 🏢 Corporate & Operations

- **System Hub** — a centralized portal where personnel reach the subsystems relevant to
  their role.
- **Kanban Task Board** — column lanes with subtasks, attachments, and automated audit logs.
- **Inventory System** — full asset lifecycle with cross-office fulfillment, bulk actions,
  and multi-format ledger exports. *(Mobile-first: asset cards, QR access, field-friendly.)*
- **Passbook / ATM Release Management** — SLA-tracking dashboard with color-coded status
  badges and live "collection index" updates.
- **Customer & Loan Ledger** — real-time account views, deep transaction history, and
  absolute balance calculations.
- **ILV & Pension Processing** — digital encoding of Initial Loan Vouchers and cross-checks
  against the central GSIS/Old-Age pensioner database with full-name search.
- **Leave Management** — accrual tracking, shared calendars, and admin/HR approval flows.
- **Internal Chat** — image sharing, reactions, read receipts, and presence.
- **Public Loan Document Generator** — throttled guest form producing branded PDFs.

### 📢 Marketing & Sales

- **Analytics Dashboard** — conversion rates, branch efficiency, and office KPIs with
  drill-down filtering.
- **Client Inquiries** — a lead pipeline (encoder → assigned → credited) with a two-level
  checker workflow and cross-branch endorsements.
- **Field Saturation** — mobile-friendly ground-crew logs with GPS coordinates and photo
  uploads for house-to-house visits.
- **Campaigns & Promos** — lifecycle tracking, file-check verification, and conversion
  metrics.
- **Agent Management & Clusters** — CRG agent distribution across branches, clusters, and
  territories.
- **Location Hierarchy** — a shared 5-level Branch → Office → Municipality → Barangay →
  Purok geography reused across every module.

### 👥 HR & People Operations

- **My Attendance** — employee self-service for biometric punches, shift history, and
  discrepancy flags.
- **Memorandums & Team Conduct** — internal distribution and progressive disciplinary records.
- **201 Files** — digital employee depository (applications, contracts, validations) with
  PDF export.
- **Public Job Applications Portal** — career funnel with image handling, digital
  signatures, and auto-generated candidate profiles.
- **Trainee Evaluations** — onboarding scoring with automated monthly-anniversary reminders.

### 🖥️ Administration & Infrastructure

- **Biometrics Manager** — a central console that monitors remote fingerprint devices and
  dispatches commands in real time through the branch agents.
- **Branch Hierarchy** — chain-of-command scopes with recursive subordinate resolution.
- **Users & Permissions** — provisioning, forced security adjustments, and authorization
  profiles mapped to per-button visibility.
- **Server Health Monitor** — storage, memory, and database-backup validation.
- **System Activity Logs** — an immutable audit stream recorded across every mutation.

---

## Technical Architecture

### Web Application Core

- **Laravel 13 / PHP 8.3+** with full-page Livewire 4 components (`#[Layout('layouts.app')]`)
  across 20 module directories — high-fidelity reactive components with minimal raw JS.
- **Alpine.js** for client-side state and UI behavior; **Tailwind CSS 3** for a density-aware,
  mobile-first design system.
- **Supporting libraries**: Tom Select (searchable multi-selects), Spotlight.js (lightbox),
  Chart.js (analytics), Tribute (mentions), xlsx (spreadsheet export), toastr (feedback).

### Real-Time Layer

- Broadcasting via **Laravel Echo** on **Pusher** (production) or **Laravel Reverb** (local).
- Channel authorization is deliberately decoupled from Eloquent to avoid relationship
  caches and keep authorization fast.
- Real-time surfaces: private chat & presence, per-device biometric command dispatch and
  log streaming, device online/offline state, and live collection-index refresh.

### Custom Authorization (RBAC)

Built on `role_user` / `permission_user` pivot tables matching on `slug` **or** `name` —
deliberately not a package drop-in:

- `hasPermissionTo()` merges role + direct permissions; `hasAnyRole()` matches pipe-separated
  role strings.
- `getAllSubordinateIds()` recursively traverses the manager tree (cluster head → branch
  manager → assistant).
- ~90 permission slugs across 14 groups; a separate seeder maps UI actions to slugs for
  per-button visibility.

### Security

- **Custom two-factor authentication** (no third-party package).
- Registration disabled; throttled login and public forms.
- **Session fingerprinting** (logs out on User-Agent change), **security headers** on every
  response, and **soft deletes** on users.
- Middleware chain enforced in `bootstrap/app.php`: global activity logger, password-change
  enforcer, fingerprint verifier, soft-delete guard, security headers, and AJAX-aware auth
  redirects.
- Biometric agents authenticate to the API with per-device API keys.

### Data Layer

- **PostgreSQL** (production) with `pg_trgm`-powered fuzzy search on pensioner data; SQLite
  for local development.
- Legacy pension system accessed through a dedicated DB connection, migrated into the modern
  schema via artisan commands.
- Notable schemas: biometric records with composite-unique integrity, offices with a custom
  primary key, JSON audit footprints on invoices, asset detail tables for 31 asset types, and
  denormalized search columns for fast lookups.

---

## Edge & Automation Engineering

### Branch Desktop Tooling (Python)

A suite of PyInstaller-compiled, windowed binaries that interact directly with fingerprint
hardware over the LAN:

- **Headless background agent** — a single-instance daemon (Win32 mutex) that polls the
  backend for commands, pushes attendance, auto-recovers the device, and **self-updates its
  own binary**.
- **Adaptive polling** — 60s during peak windows, 600s off-peak, interrupted instantly when a
  WebSocket command arrives.
- **A "universal" hardware adapter** — one adapter handles both major device vendors,
  including hand-packing proprietary protocol buffers where the standard library breaks.
  Device discovery falls back to a 100-worker parallel sweep of local subnets.
- **Trust-but-verify writes** — users are pushed, re-read, and count-verified; failed PINs
  retried with hardware cooling delays to protect device flash memory.
- **Management console GUI** — a dual-pane *Live Cloud vs Local Hardware* sync dashboard with
  a 10-finger enrollment grid, device settings editor, and an in-app terminal.
- **Fully offline device manager** — a CSV-master tool with upload/harvest arrows, template
  backup/restore, encoding selectors, and de-fragmentation of a broken vendor read path.
- **Safety discipline** — CSV backups before destructive operations, device disable/enable
  around bulk changes, and crash reports posted back to the server.

### Cloud Branch-Sync Service (Flask)

- Authenticated delta-sync hub for branch PCs: a client posts a file manifest, the server
  diffs by size + mtime, and streams back only what's missing.
- Every sync is **mirrored to off-site object storage** as the durable store — local disk is
  only a cache.
- A manager dashboard drives update distribution, force-sync queues, smart pulls, and
  full-branch downloads.

### Data Migration & Reconciliation (Laravel)

- **Legacy migration** — tens of thousands of rows from the legacy pension DB (including old
  DBF tables) into the modern schema: chunked processing, per-row fault isolation, and status
  normalization.
- **CSV import pipelines** with real-world repair logic — encoding repair, suffix-aware name
  parsing, misplaced-column realignment, and scientific-notation phone repair.
- **Reconciliation audits** — e.g., a deposit-ledger auditor that reconstructs balances from
  transactions and fails the build on drift.
- **Scheduled jobs** — device health monitoring, automated monthly rent billing with
  advance-payment dedupe, birthday/evaluation reminders, log pruning, and reservation cleanup.

---

## Tech Stack

| Layer | Technologies |
|---|---|
| Backend | PHP 8.3+ · Laravel 13 · Livewire 4 · Artisan (21 commands) |
| Frontend | Blade · Alpine.js · Tailwind CSS 3 · Vite 6 · Tom Select · Chart.js |
| Data | PostgreSQL · SQLite (dev) · legacy MySQL/DBF imports |
| Real-time | Pusher (prod) · Laravel Reverb (dev) · Laravel Echo |
| Queues | Database-driven queue workers |
| Edge hardware | Python 3.13 · PyInstaller · customtkinter · pyzk · ZKTeco/NGTeco devices |
| Automation | Flask (branch sync) · AWS S3 (backups) · cron/scheduler |
| Ops | Docker (agent container) · bash deploy script · GitHub Actions |

---

## Developer Setup

```bash
# 1. Clone and install
composer install
npm install

# 2. Environment
cp .env.example .env
#    configure DB_* / PUSHER_* (or Laravel Reverb for local),
#    APP_TIMEZONE=Asia/Manila

php artisan key:generate

# 3. Database + seed
php artisan migrate:fresh --seed
php artisan storage:link

# 4. Assets
npm run build      # production build
npm run dev        # Vite dev server with HMR

# 5. Run the stack (server + queue + websocket + logs concurrently)
composer dev
```

### Testing

```bash
composer test    # config:clear && php artisan test
```

---

## Repository Layout

```
├── app/
│   ├── Console/Commands/      # 21 artisan commands (imports, audits, reminders)
│   ├── Http/Controllers/      # 76 controllers incl. hardware-agent + ADMS surfaces
│   ├── Livewire/              # ~88 components across 20 modules
│   ├── Models/                # ~105 models, sub-namespaced by domain
│   ├── Services/              # chat, attendance, parsing services
│   └── Traits/                # auditable-actions trait
├── bootstrap/app.php          # middleware / config entrypoint (Laravel 11+ pattern)
├── database/migrations/       # 208 migrations (2025 → 2026)
├── routes/                    # web / api / auth / channels / console
├── resources/views/           # 254 Blade views + Livewire partials
├── cloud_server.py            # branch delta-sync service (Flask, S3-backed)
├── local_agent.py             # headless biometric agent (Windows daemon)
├── management_console.py      # cloud-sync management GUI
├── ZKTecoCentral.py           # offline CSV-master device manager
├── *.spec                     # PyInstaller build definitions
├── deploy.sh                  # production deploy script
└── Dockerfile                 # containerized Python agent
```

---

## Notes for Reviewers

- **Mobile-first is a design requirement** in this project, not a phase: every modified UI is
  built for ~320px first and progressively enhanced to desktop.
- **Authorization was engineered custom** rather than adopted — the pattern is documented
  above so evaluators can inspect the decision-making.
- Production data, credentials, and internal URLs are intentionally omitted from this
  repository. Contact the author for a live walkthrough or an in-depth technical review.