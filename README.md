# APC Integrated Business Systems (APCHub)

[![PHP](https://img.shields.io/badge/PHP-8.3+-%23777BB4?logo=php&logoColor=white)](https://www.php.net)
[![Laravel](https://img.shields.io/badge/Laravel-13.x-%23FF2D20?logo=laravel&logoColor=white)](https://laravel.com)
[![Livewire](https://img.shields.io/badge/Livewire-4-%23fb70a9)](https://livewire.laravel.com)
[![Tailwind CSS](https://img.shields.io/badge/Tailwind_CSS-3-%2306B6D4?logo=tailwindcss&logoColor=white)](https://tailwindcss.com)
[![Python](https://img.shields.io/badge/Python-3.13-%233776AB?logo=python&logoColor=white)](https://www.python.org)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-%234169E1?logo=postgresql&logoColor=white)](https://www.postgresql.org)

An enterprise multi-system platform that centralizes corporate operations, financial services, field marketing, human resources, biometric timekeeping, and hardware infrastructure for a multi-branch financial institution. The platform is engineered as **three interlocking systems**:

1. **Backend-driven web application** — a TALL Stack (Tailwind, Alpine, Laravel, Livewire) ERP with real-time reactive interfaces.
2. **Automated desktop compilation tools** — a suite of PyInstaller-compiled Windows GUI/agent binaries for biometric hardware management.
3. **Data reconciliation & automation scripts** — legacy-data migration commands, CSV import pipelines, ledger auditors, and a Flask branch-sync server with S3 backups.

```
┌─────────────────────────────────────────────────────────────────────┐
│                    BRANCH OFFICES (Windows PCs)                      │
│                                                                     │
│  ┌─────────────────────┐   ┌──────────────────────────────────────┐ │
│  │  ZKTeco/NGTeco      │──▶│  Desktop Tools (PyInstaller .exe)    │ │
│  │  Biometric Hardware │   │  local_agent (daemon)                │ │
│  │  (TCP :4370)        │◀──│  Management Console (GUI)            │ │
│  └─────────────────────┘   │  ZKTecoCentral / ZKTecoOnline (GUI)  │ │
│                            └───────────────┬──────────────────────┘ │
└────────────────────────────────────────────┼────────────────────────┘
                                             │ HTTPS (Sanctum/API key)
                                             │ WebSocket (Reverb/Pusher)
                 ┌───────────────────────────▼───────────────────────────┐
                 │       APCInventory — Laravel 13 Web Backend           │
                 │  RBAC · Livewire 4 · Realtime · PostgreSQL · Queues   │
                 └───────────────┬───────────────────┬───────────────────┘
                                 │                   │
                     HTTP (X-API-KEY)         DB connections
                 ┌───────────────▼───────┐   ┌────────▼────────┐
                 │  cloud_server.py      │   │  mysql_legacy   │
                 │  Flask (AWS/Ubuntu)   │   │  ILV pension    │
                 │  branch updates ·     │   │  system (legacy)│
                 │  delta sync · S3      │   └─────────────────┘
                 └───────────────────────┘
```

---

## Table of Contents

- [System 1 — Backend-Driven Web Application](#system-1--backend-driven-web-application)
  - [Core Features By Department](#core-features-by-department)
  - [Architecture](#architecture)
  - [Real-Time & Broadcasting](#real-time--broadcasting)
  - [Security](#security)
  - [Data Layer](#data-layer)
- [System 2 — Automated Desktop Compilation Tools](#system-2--automated-desktop-compilation-tools)
  - [Tool Suite](#tool-suite)
  - [Shared Engineering DNA](#shared-engineering-dna)
  - [Compilation Pipeline](#compilation-pipeline)
- [System 3 — Data Reconciliation & Automation Scripts](#system-3--data-reconciliation--automation-scripts)
  - [Cloud Sync Server](#cloud-sync-server)
  - [Legacy Migration Commands](#legacy-migration-commands)
  - [CSV Import Pipelines](#csv-import-pipelines)
  - [Scheduled Jobs](#scheduled-jobs)
- [Deployment](#deployment)
- [Local Setup](#local-setup)
- [Repository Structure](#repository-structure)

---

## System 1 — Backend-Driven Web Application

APCHub is a monolithic Laravel application acting as the centralized operating engine for the enterprise. It bridges localized branch activities (ATM/passbook releases, customer loan tracking, biometric attendance) with high-level corporate oversight, automated SLA monitoring, and administrative control panels.

**Scale:** 208 migrations · ~105 Eloquent models · ~88 Livewire components · 76 controllers · 21 artisan commands · 14 broadcast events · 14 notification classes · 254 Blade views.

### Core Features By Department

#### 🏢 Core Operations
* **System Hub (APCHub)** — a centralized, secure landing portal where authenticated personnel select specialized subsystems tailored to their operational roles.
* **Kanban Task Board** — an agile workflow space with column lanes (`To-Do`, `In Progress`, `Review`, `Done`, `Blocked`), subtask management, attachments, and automated audit logs.
* **Inventory System** — full office asset lifecycle handling with cross-office request fulfillment matrices, bulk actions, and multi-format ledger exports.
* **Passbook/ATM Release Management** — a metrics dashboard tracking `TOTAL / NEW / CITD / READY / RELEASED / OVERDUE / CANCELLED` with color-coded SLA badges.
* **MP/MA Requests** — digital processing for sensitive adjustments to monthly pension amounts.
* **Customer Ledger** — real-time loan account views, deep historical transaction logs, and absolute balance calculation.
* **ILV Management** — an operational node for encoding Initial Loan Vouchers and pushing pending credit applications through structural verification.
* **Leave Management** — absence tracking, accrual balances, and shared department calendars.
* **Internal Chat** — a full chat system with image sharing, reactions, read receipts, and presence channels.
* **Loan Document Generator** — public, throttled Livewire form that produces DOMPDF loan documents.

#### 📢 Marketing & Sales
* **Analytics Dashboard** — conversion rates, branch efficiency, and office KPIs with drill-down filtering.
* **Client Inquiries** — a lead pipeline capturing prospective clients and routing cross-branch endorsements (encoder → assigned → credited, with a two-level checker workflow).
* **Field Saturation** — mobile-friendly ground-crew logs for house-to-house visits, GPS coordinates, and photo uploads.
* **Campaigns & Promos** — promotional campaign deployment with customer conversion metrics and file-check verification.
* **Agent Management** — distribution of CRG agents across branch jurisdictions, clusters, and territories.
* **Location Hierarchy** — cascading Branch → Office → Municipality → Barangay → Purok geography shared across modules.

#### 👥 Team & HR
* **My Attendance** — employee self-service workspace for biometric punches, shift history, and discrepancy flags.
* **Memorandums** — distribution framework for internal error reports, performance tracking, and company-wide memos.
* **Team Conduct** — progressive disciplinary hub for recording protocol violations.
* **201 Files** — digital depository of employee records, background validations, onboarding paperwork, and contracts (40+ field employee profiles + PDF export).
* **Job Applications Portal** — public career funnel with 2x2 professional image handling, digital signatures, and automated candidate PDF profiles.
* **Trainee Evaluations** — quantitative scoring framework for new-hire onboarding reviews with monthly-anniversary reminder automation.

#### 🏦 CITD & Pensions
* **CITD & Pensions Mainframe** — cross-verification of centralized GSIS/Old-Age pensioner data with full-name search (pg_trgm) and MP/MA letter generation.
* **Interlinked ILV Pipelines** — shared cross-department verification bridging processing teams and core pension underwriters.

#### ⚙️ Administration & Infrastructure Control
* **Biometrics Manager** — a central console monitoring remote ZKTeco appliances with live `connected / syncing / disconnected` signals driven by background Python workers.
* **Branch Hierarchy** — a multi-tiered manager relationship framework enforcing chain-of-command scopes.
* **Users Management** — user provisioning, forced security adjustments, and authorization profiles.
* **System Control Panel** — systemic configurations, department tuning, and global permissions.
* **Server Health Monitor** — storage, memory, and database-backup validation dashboard.
* **System Activity Logs** — immutable auditing stream (`LogsActivity` trait + `GlobalActivityLogger` middleware) with model-type/ID/status enrichment and detail-tag views.

### Architecture

#### TALL Stack Frontend
* **Laravel 13 / PHP 8.3+** — routing, database manipulation, and complex state engines.
* **Livewire 4** — full-page components via `#[Layout('layouts.app')]` across 20 module directories; high-fidelity reactive components without raw JavaScript.
* **Alpine.js** — client-side UI animations, slide-overs, dropdown states, and instant toggles.
* **Tailwind CSS 3** — a utility-first design system for high-density dashboard layouts.
* **Tom Select, Spotlight.js, Chart.js, TributeJS, toastr, xlsx** — searchable multi-selects, lightboxes, charts, mentions, and export tooling.

#### Custom RBAC
Despite `spatie/laravel-permission` being installed, authorization is a **custom system** built on `role_user` / `permission_user` pivot tables matching on `slug` **or** `name`:

* `User::hasPermissionTo()` merges role + direct permissions; `User::hasAnyRole()` matches pipe-separated role strings.
* `User::hasGlobalAccess()` covers admin / inventory-manager / auditor.
* `User::getAllSubordinateIds()` recursively traverses the manager tree (cluster head → branch managers → assistants).
* ~90 permission slugs across 14 groups (`RolesAndPermissionsSeeder`); `PageActionSeeder` maps UI actions to permission slugs for per-button visibility.

#### Middleware Stack (`bootstrap/app.php`)
| Middleware | Purpose |
|---|---|
| `GlobalActivityLogger` | Logs POST/PUT/PATCH/DELETE to the `logs` table |
| `EnsurePasswordIsChanged` | Forces `password_change_required` users to change their password |
| `VerifySessionFingerprint` | Logs out on User-Agent change (IP allowed to change) |
| `CheckIfUserIsSoftDeleted` | Routes soft-deleted users to an inactive-account screen |
| `AddSecurityHeaders` | nosniff, SAMEORIGIN frames, HSTS, Permissions-Policy, strips `X-Powered-By` |
| `HandleAjaxAuthenticationRedirect` | Returns 401 JSON for expired-session AJAX calls |
| `AuthenticateBiometricAgent` | Device auth via `X-Biometric-Key` / Bearer / `api_key` |
| `CheckSystemStatus` | 503 maintenance mode driven by `Setting('site_maintenance')` |

#### Key Routing Patterns
* **Route ordering is critical** — custom PUT/PATCH routes must appear before resource routes (rental invoices/tenants, marketing inquiries).
* **`/iclock/*` group** — emulates a ZKTeco ADMS server for devices that cannot run the Python agent: handshake, ATTLOG/USERINFO/FINGERTMP ingestion, command polling (`getrequest`), and result collection (`devicecmd`).
* **`/api/biometrics/agent/*`** — API-key-authenticated surface for the Python daemon: ping, records, commands, upload-users, upload-fingerprints, logs, config.
* **Route aliases** — `verified`, `role`, `auth.biometric` wired in `bootstrap/app.php`.

### Real-Time & Broadcasting
Pusher (prod) / Laravel Reverb (dev, port 8080), consumed via Laravel Echo. Channel authorization in `routes/channels.php` **bypasses Eloquent with raw `DB::table()`** to avoid relationship caches:

| Channel | Type | Use |
|---|---|---|
| `App.Models.User.{id}` | Private | Per-user notification + chat delivery |
| `conversation.{id}` | Private | Chat messages, reactions, reads |
| `biometric.device.{id}` | Private | Agent command dispatch, sync progress, device logs |
| `biometrics-status` | Private | Device online/offline, user scan results |
| `chat` | Presence | Online user roster |
| `passbook-releases` | Public | Collection index live refresh |

14 broadcast events (most `ShouldBroadcastNow` for synchronous delivery), including `DeviceCommandDispatched`, `BiometricDeviceStatusUpdated`, `MessageSent`, and `PassbookReleaseUpdated`.

### Security
* **Custom 2FA** — dedicated `two_factor_code` / `two_factor_expires_at` columns (no package).
* **Registration disabled**; throttled login, password reset, and public forms.
* **Soft deletes** on `User`; photo storage in `storage/app/public/`.
* **Session fingerprinting** and **security headers** on every web response.
* **CSRF exceptions** only for `/iclock/*`, `/api/biometrics/agent/*`, and `broadcasting/auth`.

### Data Layer
* **PostgreSQL** (prod) with pg_trgm-powered pensioner search; **SQLite** for local dev.
* **`mysql_legacy` connection** — the legacy ILV pension system (`ilvapplication` DB), plus a separate `DB_ILV_*` PostgreSQL connection for the modern ILV module.
* Notable schemas: `biometric_records` (composite unique on device+user+timestamp), `offices` (custom PK `OfficeID`), `inventory_summary` (DB view), `invoices` (JSON audit footprints), asset detail tables for 31 asset types.
* **Recurring invariants:** `ltrim($id, '0') ?: '0'` biometric PIN normalization, explicit `'OfficeID'` keys on every Office relationship, and denormalized search columns (e.g., `full_name_search`) for lookups.

---

## System 2 — Automated Desktop Compilation Tools

A suite of Windows tools compiled with **PyInstaller** that manages ZKTeco/NGTeco fingerprint time-attendance hardware at physical branches and syncs them to the Laravel backend (`https://www.teamapcsolutions.com`). They share a universal hardware adapter, robust write-verify loops, and a consistent dark-mode UI.

### Tool Suite

| Tool | Type | Purpose | Backend dependency |
|---|---|---|---|
| `local_agent.py` | Headless daemon | Production "set-and-forget" agent at each branch PC: polls commands, pushes attendance, auto-recovers the device, self-updates | API key + Reverb WebSocket |
| `management_console.py` | Full cloud-sync GUI | Dual-pane LIVE CLOUD vs LOCAL HARDWARE sync dashboard with 10-finger enrollment grid | Sanctum token + web session |
| `ZKTecoOnline.py` | Lean cloud-sync GUI | Offline-tolerant fork; boots straight to the dashboard, degrades gracefully when the cloud is unreachable | API key |
| `register_tool.py` | Cleaned distribution fork | Comment-stripped copy of the management console used as the registration-tool build source | Sanctum token + web session |
| `ZKTecoCentral.py` | Fully offline GUI | "APrime Credit Biometric Manager" — master CSV database as source of truth; Upload/Harvest sync to devices or files | **None** |

#### `local_agent.py` — Headless Background Agent (852 lines)
The production daemon, enforced as a single instance via a Win32 `CreateMutexW` mutex:
- **`SmartDeviceAdapter`** — a "universal router" for ZKTeco AND NGTeco devices. NGTeco devices are detected via `get_platform()` containing `'ZLM'` and handled by hand-packing 120-byte protocol buffers with `struct.pack_into`, bypassing broken pyzk paths.
- **Adaptive polling** — 60s during peak hours (5–9h, 15–20h), 600s off-peak, escaped instantly by a `threading.Event` when a WebSocket command arrives.
- **`connect_with_fallback()`** — multi-stage device recovery: primary IP → gateway-mismatch `.0`↔`.1` swap → 100-worker `ThreadPoolExecutor` sweep of all local subnets for TCP port 4370, persisting the discovered IP.
- **11 server-issued hardware commands** — `RESTART`, `UPDATE_USERS`, `DELETE_USER`, `SYNC_TIME`, `FULL_SYNC`, `WIPE_DEVICE`, `SELF_UPDATE`, `UPDATE_CONFIG`, etc.
- **Trust-but-verify writes** — pushes users, re-reads `get_templates()`, confirms count parity, retries failed PINs up to 3 times with hardware cooling delays (2s per 20 users).
- **Self-updating binary** — downloads the new `.exe`, hands off to a `.bat` that atomically replaces and relaunches the process.
- **Crash reporting** — a global `sys.excepthook` posts `AGENT CRASHED` stack traces to the server.
- **Safety discipline** — CSV backups to `attendance_backups/` before destructive `clear_attendance()`, `disable_device()`/`enable_device()` around bulk ops.

#### `management_console.py` — Cloud-Sync Management Console (1311 lines)
The richest GUI tool, built on **customtkinter** (dark/blue) with styled `ttk.Treeview` sync matrices:
- Dual-pane **LIVE CLOUD SERVER** vs **LOCAL HARDWARE** comparison with green/red sync-state tagging.
- **Dual auth**: `POST /api/login` for a Sanctum token + CSRF-scraped web session cookie for web-authenticated routes.
- **Device settings editor** — reads/writes network params (IP/mask/gateway) and ADMS/Cloud options via pyzk, with "Save & Reboot Device".
- **10-finger enrollment grid** — buttons per finger slot (0–9) showing green ✔ enrolled / grey + empty; live hardware enrollment with "press finger 3 times" dialogs.
- **Thread→UI marshalling** via `self.after(0, ...)` and a `TextRedirector` that pipes `stdout`/`stderr` into an on-screen terminal.
- Port-4370 conflict detection ("stop `APCBiometricService` in Windows Services"), "Connection Desynced" hints, and singleton editor windows.

#### `ZKTecoCentral.py` — Offline Credit Biometric Manager (928 lines)
A server-independent tool using `central_db.csv` as a master database:
- **Upload >>>** (Central → Target) and **<<< Harvest** (Target → Central) sync arrows, where the target is a live device *or* a CSV file.
- **NGTeco de-fragmentation** — pyzk returns one real user as multiple fragmented 120-byte rows; the tool filters known artifact UIDs, detects swapped name/ID fields, strips `NN-` prefixes and control-character garbage, and merges fragments by clean numeric ID.
- **Encoding selector** (UTF-8 / GBK / GB2312 / latin-1) and `clean_zk_str()` sanitization.
- Full 10-template backup/restore to CSV (base64-encoded `Finger` objects).

### Shared Engineering DNA
- **`SmartDeviceAdapter`** — the ZKTeco + NGTeco universal protocol shim (NGTeco raw-buffer packing).
- **Leading-zero normalization** — `ltrim('0') or '0'` on every PIN pushed or pulled, matching the backend convention.
- **Trust-but-verify write loops** with reconnect retries and hardware cooling delays to protect device flash memory.
- **100-worker parallel subnet scans** for device discovery.
- **Frozen-aware config loading** — `config.json` / `version.txt` / `aprimeicon.ico` load from beside the `.exe`.
- Safe deletes via `delete_user(uid=int(uid))` to dodge pyzk's string-concat crash bug.

### Compilation Pipeline
PyInstaller **one-file, windowed** builds (`console=False`, `upx=True`) via `.spec` files:

| Spec file | Entry script | Output | Icon |
|---|---|---|---|
| `ZKTecoCentral.spec` | `ZKTecoCentral.py` | `ZKTecoCentral.exe` (~31 MB) | `aprimeicon.ico` |
| `ZKTecoOnline.spec` | `ZKTecoOnline.py` | `ZKTecoOnline.exe` (~17 MB) | `aprimeicon.ico` |
| `ZKTeco Manager v.2.spec` | `ZKTecoCentral.py` | `ZKTeco Manager v.2.exe` | — |
| `APC_Launcher.spec` | `launcher.py` (legacy) | `APC_Launcher.exe` | — |

```bash
# Example build for a desktop tool
pyinstaller --clean --noconfirm ZKTecoOnline.spec
```

The legacy branch deployment used an Inno Setup installer (`APC_System_Update.exe`) that silently deployed NSSM-registered Windows services (`APCBiometricService`, `APCPatcher`) — since superseded by `local_agent.py`'s self-updating single-instance daemon. Build artifacts live in `build/` and `dist/` (built with Python 3.13).

---

## System 3 — Data Reconciliation & Automation Scripts

A set of scripts that migrate, reconcile, and audit data between the modern PostgreSQL backend, the legacy MySQL ILV pension system, CSV exports, hardware devices, and an AWS S3-backed cloud file store.

### Cloud Sync Server
`cloud_server.py` (Flask, deployed on AWS/Ubuntu) is the branch file-sync hub — the durable backing store for a compiled branch launcher. Authenticated via `X-API-KEY` (branch secret vs. manager password) with on-disk state files (`known_branches.json`, `aliases.json`, `syncing_branches.json`, `force_sync_queue.json`).

| Endpoint | Purpose |
|---|---|
| `/launcher/check_update/<branch>` | Registers branch, reports if a `{branch}_update_*.zip` exists |
| `/launcher/download_update/<filename>` | Streams the update zip (path-traversal safe) |
| `/launcher/ack_update/<filename>` | Consumes the zip, records `last_update.txt` |
| `/launcher/sync/manifest/<branch>` | **Delta sync pt. 1** — client posts file manifest; server diffs by size+mtime |
| `/launcher/sync/upload/<branch>` | **Delta sync pt. 2** — receives the zip, merges the manifest, **mirrors to S3**, clears the local cache |
| `/launcher/check_force_sync` / `ack_force_sync` | Manual force-sync queue (polled and acknowledged by branches) |
| `/launcher/admin/status` | Manager dashboard of all branches, pending updates, sync dates |
| `/launcher/admin/smart_pull/<branch>` | Manager pulls only the files it's missing as a delta zip |
| `/launcher/admin/upload_update/<filename>` | Manager pushes a new update package |
| `/launcher/admin/download_branch_data/<branch>` | Full branch LocalFiles download as a timestamped zip |
| `/launcher/admin/clean_updates` | Deletes update zips older than 7 days |

**S3 backup flow:** every delta upload triggers `upload_folder_to_s3()` into bucket `aprime-localfiles-s3` (`ap-southeast-1`) under `branch_data/{branch}/LocalFiles/` plus `manifest.json` — S3 is the durable store, local disk only a cache. Branch IDs are normalized through `clean_branch_id()` ("The Bouncer"): alias mapping → first run of digits.

### Legacy Migration Commands
Artisan commands migrating the legacy MySQL ILV pension system (`ilvapplication` DB) into the modern PostgreSQL schema:

| Command | Source → Destination | Notes |
|---|---|---|
| `import:legacy-ilv` | `tblilvapplication` → `ilv_pensioners` / `ilv_loan_applications` / `ilv_co_makers` | ~40k rows, `chunk(500)`, per-row fault isolation, status normalization |
| `import:legacy-receipts` | `tblsummaryreceipt` → `ilv_transactions` | Payment/RS/CASH-OUT rows become credits; loan headers become debits; per-chunk pensioner ID maps |
| `import:legacy-vouchers` | DBF tables `lclalvdetails` / `lclepvdetails` / `lclcheckcblvdetails` → `ilv_transactions` | Always debits |
| `import:csv-vouchers` | CSVs in `storage/app/legacy/` → `ilv_transactions` | Auto-detects `,` vs `;` delimiter, in-memory custcode map |
| `legacy:migrate-media` | `tblilvpensionerpics` / `tblilvcomakerpics` BLOBs → `storage/app/public/` | Photos/signatures with collision-safe filenames |

### CSV Import Pipelines
| Command | Purpose |
|---|---|
| `import:gsis` / `import:old-age` | GSIS & Old-Age pensioner masterlists → `pensioners` (streaming `fgets`, chunks of 500, single transaction, rollback) |
| `pensioners:populate-search` | Backfills the `full_name_search` denormalized column via the model's `saving` event |
| `marketing:import-clients` | Historical client CSV with `NameParser` (suffixes, comma/space formats), Windows-1252→UTF-8 encoding repair, eñe normalization, dedupe on `(first,last)` |
| `marketing:import-inquiries` | Strict positional mapping of messy inquiry columns; scientific-notation phone repair (`9.66E+09`); office resolution by name/ID with branch fallback |
| `marketing:clean-csv` | Two-phase heuristic preprocessor that realigns shifted CSV columns into `Date, Branch Code, Client Name, Contact, Address, Remarks` using regex + Tagalog keyword classification |
| `import:employee-details` | HR CSV → `users` + `employee_details`, auto-creating users with temp passwords |
| `import:locations` | 5-level Branch → Office → Municipality → Barangay → Purok hierarchy with in-memory dedupe caches |

### Reconciliation & Auditing
| Command | Purpose |
|---|---|
| `rental:audit-deposits` | Reconstructs each tenant's `deposit_balance` from invoices/payments (DEPOSIT-line credits, apply-to-deposit debits, refunds, overage logic) and flags drift; exits `FAILURE` on mismatch |
| `devices:check-status` | Marks biometric devices `disconnected` after 3 minutes of silence and broadcasts the change (every minute) |
| `zk:set-admin` | Queues a device command to promote a user to Super Admin (privilege 14) on ZKTeco hardware |
| `fingerprint_reducer.py` | Trims device memory to allowed finger slots (`fids 3–6`) so large user bases can sync |
| `rebuilt_id_bio.py` | Recreates device users whose PINs lost leading zeros (e.g., `3020001` → `03020001`), keyed by user ID not name |

### Scheduled Jobs
Defined in `routes/console.php`:

| Schedule | Job |
|---|---|
| Every minute | `devices:check-status` |
| Daily 00:00 | Prune device logs to the latest 1000 per device |
| Daily 01:00 | `rental:generate-invoices` — automated monthly billing with advance-payment dedupe, "Carrots" water-bill special case, and audit footprints |
| Daily 07:00 | `notify:birthdays` (0/3/7-day thresholds to HR/admins) |
| Daily 08:00 | `evaluations:send-reminders` (trainee evaluations 7 days before monthly hire anniversaries) |
| Hourly | Expire unused `app_number_reservations` |

---

## Deployment

### Laravel Web App (`deploy.sh`)
```
git stash → git pull origin master → stash pop →
composer install --no-dev --optimize-autoloader →
npm install && npm run build →
config:cache + route:cache + view:cache
```
No CI/CD. PostgreSQL in production; SQLite for local dev.

### Branch Computers
1. Install the compiled agent binary (or the legacy Inno Setup installer) on the branch PC.
2. Fill `config.json` beside the executable with `server_url`, `api_url`, `api_key`, `device_ip`, `device_port`, and `reverb_*` values.
3. Start `local_agent.exe` once — a Win32 mutex enforces a single instance; the agent self-recovers the device, backs up attendance to CSV, and self-updates.
4. Technicians use the Management Console (cloud-sync GUI) for bulk user/fingerprint synchronization.

### Cloud Server
Run `cloud_server.py` on the AWS/Ubuntu host with the S3 bucket configured; both API secrets set at module level. State is persisted to on-disk JSON; S3 is the durable store.

---

## Local Setup

```bash
# 1. Clone and install
git clone <repository-url> && cd APCInventory
composer install
npm install

# 2. Environment
cp .env.example .env
#    configure DB_* / PUSHER_* (or use Reverb for dev),
#    APP_TIMEZONE=Asia/Manila, VITE_PUSHER_APP_KEY, VITE_PUSHER_APP_CLUSTER

php artisan key:generate

# 3. Database + sample data
php artisan migrate:fresh --seed
php artisan storage:link

# 4. Assets
npm run build          # or: npm run dev

# 5. Serve (dev: server + queue + websocket concurrently)
composer dev
#    -or- run individually:
php artisan serve
php artisan queue:listen --tries=1
php artisan reverb:start
```

### Testing
```bash
composer test   # config:clear && php artisan test
```

---

## Repository Structure

```
├── app/
│   ├── Console/Commands/      # 21 artisan commands (imports, audits, reminders)
│   ├── Http/Controllers/      # 76 controllers incl. ADMS + biometric agents
│   ├── Livewire/              # ~88 components across 20 modules
│   ├── Models/                # ~105 models (sub-namespaced by domain)
│   ├── Services/              # Chat, Attendance, NameParser
│   └── Traits/                # LogsActivity
├── bootstrap/app.php          # middleware/config entrypoint (Laravel 11+ pattern)
├── database/migrations/       # 208 migrations (2025-06 → 2026-07)
├── routes/                    # web / api / auth / channels / console
├── resources/views/           # 254 Blade views + Livewire partials
├── cloud_server.py            # Flask branch-sync server (S3-backed)
├── local_agent.py             # Headless biometric daemon
├── management_console.py      # Cloud-sync management GUI
├── ZKTecoCentral.py           # Offline CSV-master biometric manager
├── ZKTecoOnline.py            # Offline-tolerant cloud-sync GUI
├── register_tool.py           # Distribution build of the management console
├── fingerprint_reducer.py     # Finger-slot memory optimization script
├── rebuilt_id_bio.py          # Leading-zero PIN repair script
├── *.spec                     # PyInstaller one-file build definitions
├── config.json                # Shared agent/tool configuration
├── deploy.sh                  # Laravel production deploy script
└── Dockerfile                 # Python agent container (not Laravel)
```
