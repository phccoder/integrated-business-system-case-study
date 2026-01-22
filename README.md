# Integrated Business Management System (IBMS)

A robust, multi-module enterprise application architected to centralize disparate business operations into a single, cohesive platform. It unifies Inventory, Communication, HR/Biometrics, and Marketing workflows using a modern, event-driven architecture powered by Laravel 11, PostgreSQL, and real-time WebSockets.

---

## 🏗️ System Architecture

The IBMS operates on a **Monolithic Core with Satellite Services** architecture. While the main business logic resides in a centralized Laravel application, specific hardware-dependent tasks (like Biometrics) are offloaded to distributed agents to ensure resilience and performance.

### 1. The Core (Laravel Monolith)
At the center is a **Laravel 11** application serving as the source of truth.
* **Database Layer:** Uses **PostgreSQL** for its superior handling of complex queries, JSON data types, and concurrent write operations required by the chat and logging systems.
* **Authentication & Authorization:** Implements a rigorous **RBAC (Role-Based Access Control)** system using Laravel Gates and Policies. Permissions are granular (e.g., `view-inventory`, `approve-outs`), allowing for dynamic role creation without code changes.
* **Event Bus:** Utilizes Laravel's Event/Listener pattern to decouple actions. For example, when an "Out" request is created, an event fires that simultaneously triggers a database notification, a WebSocket broadcast, and an audit log entry.

### 2. Real-Time Communication Layer (WebSockets)
Unlike traditional polling applications, IBMS uses a persistent WebSocket connection for instant updates.
* **Server:** **Laravel Reverb**, a first-party, high-performance WebSocket server optimized for Laravel.
* **Client:** **Laravel Echo** listens for private and presence channels.
* **Use Cases:**
    * **Chat:** Messages are pushed instantly to recipients.
    * **Device Status:** The dashboard updates biometric device connectivity status (Online/Offline/Syncing) in real-time without page reloads.
    * **Notifications:** Alerts for approvals or system errors appear immediately.

### 3. The Distributed Biometric Agent (Python Satellite)
To bridge the gap between the web server and physical ZKTeco hardware (which often sits behind local firewalls), a custom **Python Agent** was developed.
* **Architecture:** Decoupled "Pull-Push" mechanism.
* **Workflow:**
    1.  **Pull:** The Python script connects to the ZKTeco device over the local network using the `pyzk` protocol.
    2.  **Buffer:** Attendance logs are temporarily saved to a local CSV backup to prevent data loss during internet outages.
    3.  **Push:** The agent authenticates with the Laravel API and pushes the logs in batches.
    4.  **Heartbeat:** The agent sends a "ping" every few seconds. If the Laravel server stops receiving pings, a scheduled task automatically marks the device as "Offline" on the dashboard.

### 4. Frontend Architecture (Hybrid SPA/MPA)
The application uses a hybrid approach to balance SEO/initial load speed with interactivity.
* **Blade Templates:** Handles the routing and initial page rendering (Server-Side Rendering).
* **Alpine.js:** Provides reactive, "Vue-like" interactivity for complex UI components (Modals, Dropdowns, Dynamic Forms) without the overhead of a full SPA build step.
* **Tailwind CSS:** Ensures a consistent, utility-first design system that is highly maintainable and responsive.

---

## 🧩 Module Breakdown

### 📦 Inventory System
* **Logic:** Implements a strict "Maker-Checker" workflow for stock movements ("Outs").
* **Data Structure:** Relational design linking `Items` -> `Stocks` -> `Branches`. Stock levels are aggregated dynamically to ensure accuracy.

### 💬 Chat System
* **Security:** Messages are authorized via Policy gates ensuring only participants can view conversations.
* **Performance:** Uses database indexing on `conversation_id` and timestamps to load chat history efficiently, even with thousands of messages.

### 🕒 HR & Biometrics
* **Data Flow:** `Device` -> `Python Agent` -> `API Endpoint` -> `Postgres` -> `HR Dashboard`.
* **Hierarchy:** Users are linked via a self-referencing `manager_id` on the User model, creating a tree structure for approvals (Cluster Head -> Branch Manager -> Staff).

### 📈 Marketing Saturation
* **Asset Management:** Tracks physical marketing assets (tarpaulins) using geolocation data.
* **Media Handling:** Uses **Intervention Image** to optimize and compress photo evidence uploaded by field agents, saving storage space while maintaining visual fidelity.

---

## 🛠️ Technology Stack

| Layer | Technology | Role |
| :--- | :--- | :--- |
| **Backend** | **Laravel 11** (PHP 8.2+) | Core Framework & Business Logic |
| **Database** | **PostgreSQL** | Primary Data Store (Relational & JSON) |
| **Real-Time** | **Laravel Reverb** | WebSocket Server for live events |
| **Frontend** | **Alpine.js** + **Blade** | Reactive UI & Templating |
| **Styling** | **Tailwind CSS** | Utility-First Styling |
| **Hardware Link** | **Python 3** | Local Agent for Biometric Devices |
| **Build Tool** | **Vite** | Asset Compilation & Hot Reloading |

---

## 🚀 Deployment & Scalability

* **Docker Ready:** The application can be containerized, with separate containers for the App, DB, Redis (for queues), and Reverb.
* **Queue Workers:** Heavy background tasks (like processing bulk biometric logs or generating Excel reports) are offloaded to Laravel Queues to keep the UI responsive.
* **Task Scheduling:** Laravel Scheduler handles cron jobs for "Offline Device Detection" and daily attendance summarization.

---

## 🔐 Security Measures

* **CSRF Protection:** All forms and API requests are protected against Cross-Site Request Forgery.
* **Sanitization:** Inputs are validated and sanitized to prevent SQL Injection and XSS.
* **Policy-Based Access:** Every controller action is authorized against a specific Policy method.
* **Forced Password Reset:** Middleware intercepts requests from users with a `must_change_password` flag, forcing a secure update flow.
