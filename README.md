# APC Integrated Business Systems (APCHub) — Case Study

[![PHP](https://img.shields.io/badge/PHP-8.4+-777BB4?logo=php&logoColor=white)](https://www.php.net)
[![Laravel](https://img.shields.io/badge/Laravel-13.31-FF2D20?logo=laravel&logoColor=white)](https://laravel.com)
[![Livewire](https://img.shields.io/badge/Livewire-4.x-fb70a9)](https://livewire.laravel.com)
[![Tailwind CSS](https://img.shields.io/badge/Tailwind_CSS-3.4-06B6D4?logo=tailwindcss&logoColor=white)](https://tailwindcss.com)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-4169E1?logo=postgresql&logoColor=white)](https://www.postgresql.org)
[![Python](https://img.shields.io/badge/Python-3.13-3776AB?logo=python&logoColor=white)](https://www.python.org)
[![Vite](https://img.shields.io/badge/Vite-6.x-646CFF?logo=vite&logoColor=white)](https://vitejs.dev)

**An enterprise platform that unifies multi-branch financial operations, HR and biometric timekeeping, field marketing, IT asset management, and real-time collaboration into three interlocking systems.**

> *This is an engineering case study of a production-grade Laravel monolith deployed across multiple branch offices with on-premises biometric hardware integration.*

## Table of Contents

- [The Challenge](#the-challenge)
- [The Solution — Three Interlocking Systems](#the-solution--three-interlocking-systems)
- [Scale & Codebase Metrics](#scale--codebase-metrics)
- [Feature Highlights](#feature-highlights)
- [Technical Architecture](#technical-architecture)
- [Edge & Hardware Integration](#edge--hardware-integration)
- [Data Reconciliation & Automation](#data-reconciliation--automation)
- [Tech Stack](#tech-stack)
- [Security & Compliance](#security--compliance)
- [Deployment & Operations](#deployment--operations)
- [Mobile-First Design](#mobile-first-design)
- [Testing](#testing)
- [Key Engineering Decisions](#key-engineering-decisions)

## The Challenge

A multi-branch financial institution operating across multiple locations faced fragmented systems that impeded operational visibility and created manual overhead:

- **Disconnected data sources** — Legacy pension and ILV databases, standalone biometric time-attendance machines per branch, and paper-based field operations with no unified view.
- **Hardware fragmentation** — Dozens of ZKTeco and NGTeco fingerprint devices across branch offices, each requiring manual configuration and attendance extraction.
- **Field operations gap** — Marketing and saturation teams needed mobile-first tooling to log inquiries, campaigns, and house-to-house visits tied to a hierarchical geographic structure.
- **Asset visibility** — IT assets across all branches lacked centralized lifecycle tracking (procurement, assignment, maintenance, repairs, transfers).
- **Manual reconciliation** — Pension data migration from legacy systems required chunked processing with per-row fault isolation and auditable reconciliation.
- **Security & access control** — Strict role-based scoping by branch, office, and cluster with manager-subordinate hierarchies was required.

## The Solution — Three Interlocking Systems

| System | Purpose | Implementation |
|---|---|---|
| **1. Web Application (APCHub)** | Central ERP unifying corporate, HR, marketing, inventory and asset management, loans, ILV and pensions, chat, and tasks. | Laravel 13 · Livewire 4 · Alpine.js · Tailwind CSS 3 · PostgreSQL |
| **2. Desktop Hardware Tools** | Branch-level biometric device management with offline capability, compiled Windows binaries that bridge proprietary TCP protocols to the web API. | Python 3.13 · PyInstaller · customtkinter · pyzk |
| **3. Cloud Sync & Automation** | Delta-sync service, legacy data migration, CSV import pipelines, reconciliation auditors, scheduled jobs, and S3-backed backups. | Flask · Laravel Artisan Commands · AWS S3 · Scheduled Tasks |

## Scale & Codebase Metrics

| Metric | Count | Notes |
|---|---|---|
| **Migrations** | 263 | Spanning 2025–2026, covering core entities, asset management, performance evaluations, biometric fingerprint transfers, and system extensions. |
| **Eloquent Models** | 129 | Namespaced by domain (AssetManagement, Marketing, Chat, Biometric, HR, Rental, Loans, CITD, ILV, and related modules). |
| **Livewire Components** | 107 | Across module directories; largest: Marketing (14), AssetManagement (11), Inventory (9), HR (9), Rental (9), Admin (8). |
| **Blade Views** | 323 | Including 26 asset specification partials, mobile-optimized layouts, and module-specific views. |
| **Controllers** | 63 | Web, API (including biometric agent endpoints), and module controllers. |
| **Artisan Commands** | 39 | Imports, migrations, audits, reminders, backups, and device utilities. |
| **Routes** | 401 | Web, API, auth, channels, and console routes with deliberate ordering of custom routes before resource routes. |
| **Broadcast Events** | 16 | Real-time device telemetry, chat messaging, passbook releases, and biometric command dispatch. |
| **Services/Middleware** | 17 Services, 15 Middleware | Middleware configured in `bootstrap/app.php`. Queue workers run via `php artisan queue:listen`. |
| **Seeders** | 23 | Including role and permission seeding, sample asset data, and system initialization data. |
| **Tests** | 62 | Feature (49) and Unit (4) tests covering RBAC, asset management workflows, biometrics, chat, rentals, and system behavior. |
| **Python Tools** | 7 | `local_agent`, `management_console`, `ZKTecoCentral`, `ZKTecoOnline`, `register_tool`, `fingerprint_reducer`, `rebuilt_id_bio`. |

## Feature Highlights

### Corporate Operations & Core ERP
- **APCHub System Portal** — Role-based landing page directing users to authorized subsystems based on assigned roles and permissions.
- **Kanban Task Board** — Hierarchy-scoped task management via `TaskAccessService`. Supports coordinators (up to three designated users with department and cluster scopes), department heads (Collection, CITD, Finance), cluster heads, branch managers, and self-assignment rules. Visibility is determined by organizational scope rather than broad permission flags.
- **Inventory Management** — Multi-office inventory with cross-office fulfillment, bulk actions, and ledger exports.
- **Asset Management (Mobile-First)** — 31 asset types with dedicated and shared detail tables, a three-step wizard form with deferred binding on specification fields, QR code generation, maintenance, repair, and transfer workflows, dashboard KPIs with indexed aggregates, and PDF exports. Designed for field use on 320-pixel and larger viewports.
- **Passbook and ATM Release Management** — SLA-tracked dashboard with live collection index updates via broadcasting.
- **Customer Ledger & Loans** — Real-time loan account views, transaction history, and absolute balance calculations.
- **ILV & Pension Processing** — Initial Loan Voucher encoding with cross-checks against GSIS and Old-Age pensioner data. PostgreSQL pg_trgm powers fuzzy full-name search. Separate ILV database connections maintain data boundaries.
- **Leave Management** — Accrual tracking, leave balances, shared calendars, and approval workflows.
- **Internal Chat** — Real-time messaging with image sharing, reactions, read receipts, and presence channels.
- **Public Loan Document Generator** — Throttled guest form generating branded DOMPDF loan documents.

### Marketing & Field Sales (Mobile-Optimized)
- **Marketing Dashboard** — Conversion rates, branch efficiency, and office KPIs with drill-downs and cached analytics with a 60-second time-to-live.
- **Client Inquiries** — End-to-end lead pipeline with encoder-to-assigned-to-credited flow and a two-level checker workflow.
- **Field Saturation** — Mobile-first ground-crew logging with GPS coordinates and photo uploads for house-to-house visits.
- **Campaigns & Promotions** — Campaign lifecycle tracking, file-check verification, and conversion metrics.
- **Agent Management & Clusters** — CRG agent distribution across branches, clusters, and territories. The Cluster Portal is restricted to users with `admin` or `marketing-head` roles.
- **Location Hierarchy** — Shared five-level geography: Branch → Office → Municipality → Barangay → Purok, reused across modules.
- **Comprehensive Reports** — Unified analytics with 12 filters, 6 tabs, and CSV exports.

### HR, People Operations & Evaluations
- **My Attendance** — Employee self-service for biometric punches, shift history, and discrepancy flags.
- **201 Files** — Digital employee repository with comprehensive fields, contracts, validations, background checks, and PDF export.
- **Job Applications Portal** — Public career funnel with 2×2 professional photos, digital signatures, and automatically generated candidate PDF profiles.
- **Trainee Evaluations** — Quantitative onboarding scoring with monthly-anniversary reminders.
- **Quarterly Performance Evaluations** — Hierarchy-scoped workspace with strict enforcement: exactly one evaluation per evaluatee per quarter via a unique database constraint. Evaluator resolution follows a defined chain (Branch Manager, fallback to Cluster Head if none; Cluster Heads evaluated by Operation Manager, otherwise HR-only). Includes an HR-exclusive Company Rules & Regulation rating folded into the computed final average, percentage, and qualitative rating. Supports card and list views, HR inline rating modal, and CSV/PDF exports. Superseded evaluations are archived.
- **Memorandums & Team Conduct** — Internal distribution, error reports, and progressive disciplinary records.

### Administration, Infrastructure & Biometrics
- **Biometrics Manager** — Central console monitoring remote devices with live connected, syncing, and disconnected states driven by Python agents and WebSocket broadcasting.
- **Branch Hierarchy** — Multi-tier manager relationships with recursive subordinate resolution for scope enforcement.
- **Users & Permissions** — Custom role-based access control with 138 permission slugs across 14 groups (Inventory, Management, Chat, Biometrics, HR, Marketing, Rental, Collections, CITD, ILV, Loans, Leave, Finance, Asset Management, External Inventory, and Suggestions).
- **System Control Panel** — Global configurations and department-level tuning.
- **Server Health Monitor** — Storage, memory, and database-backup validation dashboard.
- **System Activity Logs** — Immutable audit trail via `LogsActivity` trait and `GlobalActivityLogger` middleware. Captures model type, identifier, action, IP address, user agent, status, and details. Activity logs are retained for 90 days with scheduled pruning.
- **Biometric Fingerprint Transfers** — Fingerprint template transfer tracking between devices and users.

## Technical Architecture

### Backend Core
- **Framework**: Laravel 13.31 on PHP 8.4+
- **Full-Stack Reactivity**: Livewire 4 with `#[Layout('layouts.app')]` pattern across major pages
- **Client State**: Alpine.js 3.x with collapse and focus plugins
- **Assets**: Vite 6 with Tailwind CSS 3.4, PostCSS, and Autoprefixer
- **Queues**: Database driver (development uses `php artisan queue:listen --tries=1`)
- **Broadcasting**: Laravel Reverb (development) or Pusher (production) via Laravel Echo

### Custom Role-Based Access Control
The system implements a custom RBAC model (separate from package-based solutions) for precise organizational scoping:

```php
// Core methods on User model
hasPermissionTo($permission)  // Merges role and direct permissions
hasAnyRole($roles)            // Matches pipe or comma-separated slugs and names
hasGlobalAccess()             // Grants access for admin, inventory-manager, auditor
getAllSubordinateIds()        // Recursively traverses manager hierarchy
```

- **Pivots**: `role_user` and `permission_user` matching on `slug` or `name`
- **Hierarchies**: Cluster Head → Branch Manager → Branch Assistant, with recursive resolution
- **Task Board**: `TaskAccessService` derives view and assign rights from hierarchy plus `task_coordinators` (maximum 3) with `task_coordinator_scopes` (departments and clusters)
- **Office Scoping**: Users without `manage-assets` permission are restricted to their assigned `office_id`. Global access is granted to authorized managers. Scoping is enforced in query builders and re-validated on each request.

### Middleware Stack (`bootstrap/app.php`)
| Middleware | Purpose |
|---|---|
| `HandleAjaxAuthenticationRedirect` | Returns 401 JSON for expired AJAX sessions |
| `CheckIfUserIsSoftDeleted` | Blocks access for soft-deleted users |
| `EnsurePasswordIsChanged` | Forces password change when required |
| `VerifySessionFingerprint` | Session hijack protection based on User-Agent |
| `AddSecurityHeaders` | Applies security headers (nosniff, X-Frame-Options SAMEORIGIN, HSTS, Permissions-Policy, strips X-Powered-By) |
| `GlobalActivityLogger` | Audits POST, PUT, PATCH, and DELETE mutations |
| `AuthenticateBiometricAgent` | Authenticates biometric agents via `X-Biometric-Key`, Bearer token, or `api_key` |
| `CheckSystemStatus` | Enforces maintenance mode via `Setting('site_maintenance')` |

### Real-Time Layer
- **Channel Authorization**: Bypasses Eloquent in `routes/channels.php` using `DB::table()` to avoid relationship caching in real-time authorization checks
- **Private Channels**: `conversation.{id}`, `biometric.device.{officeId}`, `App.Models.User.{id}`
- **Presence Channels**: `chat`
- **Broadcasting Surfaces**: `biometrics-status`, `passbook-releases`
- **Events** (16 total, primarily `ShouldBroadcastNow`): `DeviceCommandDispatched`, `BiometricDeviceStatusUpdated`, `MessageSent`, `PassbookReleaseUpdated`, and chat events for messages, reactions, and read receipts

### Data Layer
- **Primary Database**: PostgreSQL (production); SQLite (local development)
- **Legacy Connections**: `mysql_legacy` (legacy pension database) and separate `DB_ILV_*` connection for the modern ILV module
- **Key Patterns**:
  - `offices` table uses custom primary key `OfficeID` — all relationships must explicitly reference this key
  - `biometric_records` has composite unique index on `[biometric_device_id, device_user_id, timestamp]`
  - `DeviceLog` has `$timestamps = false` and manages `created_at` via model boot events
  - `logs` table powers immutable activity auditing (90-day retention via scheduled pruning)
  - `performance_evaluations` has unique constraint on `(period, evaluatee_id)` enforcing one evaluation per quarter; superseded evaluations are archived to `performance_evaluations_archive`
  - pg_trgm extension supports fuzzy matching for pensioner searches
  - Biometric PIN normalization: `ltrim($id, '0') ?: '0'` applied consistently across backend and edge agents
