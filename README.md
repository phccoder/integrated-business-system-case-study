# APC Integrated Business Systems

A multi-system web application designed to centralize business operations, featuring a complete Inventory Management System, a real-time Chat System, a Biometrics & Attendance System, and a Marketing Saturation tracker. Built on a modern, secure, and scalable technology stack.

## Project Overview

This project provides a unified platform for managing distinct business systems. It is built with a focus on security, real-time interactivity, and administrative control. The initial release includes a comprehensive Inventory Management module, a full-featured team Chat module, a Biometrics & Attendance module, and a Marketing Saturation module, with a flexible architecture designed for future expansion.

## Core Features

#### 🏢 System Hub
A central landing page after login for users to select which system they want to enter (Inventory, Chat, Biometrics, etc.).

#### 🔐 Roles & Permissions System
- **Dynamic Roles:** Administrators can create and define any number of user roles (e.g., "Branch Manager", "Inventory Auditor", "HR Officer", "Cluster Head").
- **Granular Permissions:** A robust permission system controls access to every feature, including viewing pages, creating records, editing, deleting, approving requests, and exporting data.
- **Admin Management Panel:** A dedicated UI for administrators to assign multiple roles and specific permissions to users.
- **Forced Password Changes:** A secure workflow requires newly created users or users whose passwords have been reset by an admin to change their password on their first login.

#### 📦 Inventory Management System
- **Dashboard:** A role-aware dashboard that provides global KPIs for high-level users and office-specific stats for branch-level users.
- **Items Management:** Full CRUD (Create, Read, Update, Delete) functionality for inventory items.
- **Branch & Office Management:** Dedicated pages for administrators to manage company branches and offices.
- **Orders & Outs Workflow:** A complete approval workflow for "Outs" requests, allowing managers to approve or reject item requests from different offices.
- **Data Export:** Export grid data to Excel (`.xlsx`) format.

#### 📈 Marketing Saturation System
- **📍 Location & Material Tracking:** Allows marketing teams to log the specific locations where promotional materials like flyers and tarpaulins are deployed.
- **📸 Photo Evidence Upload:** Users can upload photos as proof of placement, ensuring accountability and accurate records.
- **📊 Status Monitoring & Reporting:** Track the status of each material (e.g., "Active", "Needs Replacement") and generate reports on market coverage.

#### 💬 Real-Time Chat System
- **Private & Group Conversations:** Users can engage in one-on-one private chats or be added to multi-user group conversations.
- **Real-Time Messaging:** New messages appear instantly for all participants without needing a page refresh, powered by WebSockets.
- **File & Image Uploads:** Users can securely upload and share images and documents within chats.
- **Moderation:** Users with appropriate permissions can delete messages.

#### 🕒 Biometrics & Attendance System
- **ZKTeco Device Integration:** Seamlessly pulls attendance data from ZKTeco biometric hardware.
- **Resilient Python Local Agent:** A standalone Python script acts as a local agent, fetching data from the device and pushing it to the server. Features local CSV backups and a "Pull, Push, Clear" workflow to ensure data integrity.
- **Real-Time Device Status:** The web dashboard displays the live status of each biometric device (connected, syncing, disconnected) via WebSockets.
- **Automatic Offline Detection:** A scheduled backend task automatically marks devices as "disconnected" if they haven't sent a ping in several minutes.
- **HR Attendance Dashboard:** A dedicated page for HR personnel with a searchable list of all employees with attendance records.
- **Compact Daily Summary:** Clicking an employee opens a modal showing a day-by-day summary of their attendance, complete with date filtering and an Excel export option.

#### ⚖️ HR & Offense Reporting System
- **Hierarchical Structure:** Supports a `Cluster Head > Branch Manager > Branch Assistant` user hierarchy, created via a `manager_id` relationship.
- **Cluster Head Dashboard:** A dedicated page for Cluster Heads to view their team members and create offense reports for their subordinates.
- **HR Report Management:** A dedicated page for HR to view a complete list of all submitted offense reports, see full details, and manage their status.

#### 🔔 Notification System
- **Database & Desktop Notifications:** A unified notification system alerts users to important events (e.g., new chat messages, new "Outs" requests).
- **UI Integration:** Notification dropdowns in the navigation bar with unread counts.

#### 📝 Audit Logging
All critical actions (creating items, approving requests, deleting records) are logged with details on which user performed the action and when.

## Technologies Used

- **Backend**
    - Laravel 11+, PHP 8.2+
    - Database: PostgreSQL
- **Frontend**
    - Vite: For modern, fast asset bundling.
    - Alpine.js: For reactive, declarative UI components.
    - Tailwind CSS: For a utility-first, modern design system.
- **Real-Time**
    - Laravel Reverb: A first-party, high-performance WebSocket server for Laravel.
    - Laravel Echo: Frontend library for subscribing to WebSocket channels.
- **Key Packages**
    - Laravel Breeze: For authentication scaffolding.
    - Intervention Image: For server-side image processing and compression.
    - Maatwebsite/Excel: For generating `.xlsx` reports.
- **Satellite Components**
    - Python 3: For the local biometric agent.
    - `pyzk` & `requests`: Core Python libraries for device communication and API requests.
    - PyInstaller: For compiling the Python agent into a standalone Windows executable.
