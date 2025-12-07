# final_project_creative# VSMS - Volunteer Service Management System

> **Strategic Architecture and Implementation Plan**  
> *Course Code: INFO8003 | Final Major Project*

## 📖 Introduction

**VSMS (Volunteer Service Management System)** is a comprehensive digital ecosystem designed to modernize the non-profit sector. It functions as a two-sided marketplace connecting **Organizations** with **Volunteers**, focusing specifically on **verification, compliance, and automated attendance**.

The project was conceived to solve critical pain points:
*   **For Volunteers:** Eliminating the hassle of paper "hour logs" and chasing supervisors for signatures.
*   **For Organizations:** reducing administrative burnout caused by manual scheduling and email threads.
*   **For Schools/Employers:** Providing trustworthy, verifiable proof of volunteer service.

## 🚀 Key Features

### 1. Smart Discovery & Matching 🔍
*   **Personalized Feed:** Matches volunteers with events based on location, skills, and availability.
*   **Gamification:** Progress bars tracking progress toward goals (e.g., "22/40 Hours Completed").

### 2. Geofenced Attendance Engine 📍
*   **Automated Detection:** Uses background geolocation to detect when a volunteer enters the event radius (100m).
*   **Slide-to-Check-In:** A deliberate UI interaction to prevent accidental check-ins while ensuring physical presence.
*   **Battery Optimization:** Utilizes accelerometer data to power down GPS when stationary.
*   **Offline Mode:** "Local-first" architecture allowing check-ins in remote areas with auto-sync upon reconnection.

### 3. Compliance-First Verification 📄
*   **Automated Certification:** Generates **tamper-proof PDF certificates** with one click.
*   **Ontario Standard:** Specifically formatted to match the **Ontario Secondary School Diploma (OSSD)** community involvement forms.
*   **Verifiable Credentials:** Each certificate includes a unique hash/QR code for third-party validation (preventing fraud).

## 🛠 Tech Stack & Architecture

The system follows a **Matrix Development Structure** and utilizes a modern cloud-based toolchain.

| Component | Technology |
| :--- | :--- |
| **Mobile Frontend** | React Native (iOS & Android) |
| **Backend API** | Node.js (REST API) |
| **Database** | AWS RDS (PostgreSQL) + SQLite (Local) |
| **Auth & Notifications** | Firebase Auth & Cloud Messaging (FCM) |
| **Maps & Location** | Mapbox SDK, `react-native-background-geolocation` |
| **PDF Engine** | `react-native-html-to-pdf` |
| **Design/Proto** | Figma |

### Architecture Highlights
*   **Geofencing Logic:** Implements the Haversine formula for distance calculation with anti-spoofing checks.
*   **Data Integrity:** Uses a cryptographic signing process for TimeLogs to ensure hours cannot be altered after verification.

## 👥 The Team (Group 3)

| Name | Role | Primary Responsibilities |
| :--- | :--- | :--- |
| **Chunxi Zhang** | Product Owner | Business Model, Personas, Use Cases, Agile Plan |
| **Bo Yang** | Scrum Master / QA | Process Facilitation, Geofencing Testing, Risk Mitigation |
| **Bo Zhang** | Lead Solutions Architect | Database Design (ERD), Class Diagrams, Backend Logic |
| **Jianting Liu** | Lead Mobile Dev / UX | UI/UX Design, React Native Implementation, Prototypes |

## 📅 Agile Implementation Plan

The MVP development is structured into three 3-week Sprints:

*   **Sprint 1: Foundation & Discovery** (Weeks 1-3)
    *   *Goal:* User Authentication, DB Schema, and Map-based Event Discovery.
*   **Sprint 2: The "Check-in" Engine** (Weeks 4-6)
    *   *Goal:* Background Location Services, Geofencing Triggers, and Offline Data Sync.
*   **Sprint 3: Verification & Compliance** (Weeks 7-9)
    *   *Goal:* Organization Dashboard, PDF Generation, and Security Hash Implementation.



## 📄 License

This project is developed for educational purposes (Course INFO8003).

---
*Date: 2025-12-05*