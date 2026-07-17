<div align="center">

<br>

```
                    ____  _             _            _     ____            _        _ 
                   / ___|| |_ _   _  __| | ___ _ __ | |_  |  _ \ ___  _ __| |_ __ _| |
                   \___ \| __| | | |/ _` |/ _ \ '_ \| __| | |_) / _ \| '__| __/ _` | |
                    ___) | |_| |_| | (_| |  __/ | | | |_  |  __/ (_) | |  | || (_| | |
                   |____/ \__|\__,_|\__,_|\___|_| |_|\__| |_|   \___/|_|   \__\__,_|_|
                                                                            
```

<br>

### 🎓 A complete student portal built for real schools — web, Android & iOS

<br>

[![Next.js](https://img.shields.io/badge/Next.js_15-black?style=for-the-badge&logo=next.js&logoColor=white)](https://nextjs.org/)
[![Flask](https://img.shields.io/badge/Flask_3.1-000000?style=for-the-badge&logo=flask&logoColor=white)](https://flask.palletsprojects.com/)
[![Python](https://img.shields.io/badge/Python_3.14-3776AB?style=for-the-badge&logo=python&logoColor=white)](https://python.org/)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-316192?style=for-the-badge&logo=postgresql&logoColor=white)](https://postgresql.org/)

[![Capacitor](https://img.shields.io/badge/Android_APK-119EFF?style=for-the-badge&logo=capacitor&logoColor=white)](https://capacitorjs.com/)
[![Cloudinary](https://img.shields.io/badge/Cloudinary-3448C5?style=for-the-badge&logo=cloudinary&logoColor=white)](https://cloudinary.com/)
[![Render](https://img.shields.io/badge/Backend-Render-46E3B7?style=for-the-badge&logo=render&logoColor=white)](https://render.com/)
[![Vercel](https://img.shields.io/badge/Frontend-Vercel-black?style=for-the-badge&logo=vercel&logoColor=white)](https://vercel.com/)

</div>

> Students log in, browse subjects, download study materials, and get notified the moment something important happens. Admins control everything — who gets in, what gets uploaded, what everyone sees. Works in any browser, installs as an Android app, and can be added to the home screen on iPhone.

<br>

---

<div align="center">

**Quick Navigation**

[🌍 Platforms](#-platforms) · [✨ Features](#-features) · [🏗️ Architecture](#️-architecture) · [🔒 Security](#-security) · [📂 Structure](#-folder-structure) · [🗄️ Database](#️-database-tables) · [🕵️ Analysis](#️-detailed-project-analysis--engineering-mechanics) · [🧰 Libraries](#-libraries-used) · [🤔 FAQ](#-faq)

</div>

---

## 🌍 Platforms

Built once, runs everywhere — same codebase, three platforms.

| | Platform | How it works |
|---|---|---|
| 📱 | **Android App** | Packaged into a signed `.apk` using Capacitor. Auto-built on every release. |
| 🍎 | **iPhone / iPad** | Install via Safari → "Add to Home Screen". Runs as a PWA. |

---

## ✨ Features

<details>
<summary><b>👨‍🎓 What students can do &nbsp;(click to expand)</b></summary>

<br>

- **Register & wait for approval** — no one gets in without an admin saying yes first
- **Secure Authentication** — receive OTPs and password reset links securely via email (SMTP)
- **Dashboard** — see your subjects, latest announcements, and how many unread notifications you have
- **Browse subjects** — see everything you're enrolled in for the current semester
- **Download materials** — access lecture notes, slides, and assignments uploaded by your instructor
- **Secure file viewer** — encrypted files open inside a protected in-browser viewer. No download button, no right-click, no Ctrl+P
- **Push notifications** — get notified on your phone or PC the moment you're approved, or when a new file or announcement goes up
- **Profile & devices** — update your name, see which devices are logged into your account right now
- **Support** — message the admin directly from inside the app (integrated with Resend for email replies)
- **Works offline** — the app shell loads from cache even without internet

</details>

<details>
<summary><b>🛠️ What admins can do &nbsp;(click to expand)</b></summary>

<br>

- **Approve or reject** new student registrations one by one
- **Edit user profiles** — change roles, suspend accounts, remove specific devices
- **Manage subjects & semesters** — create, edit, archive when the term ends
- **Upload files** — with automatic watermarking on PDFs/images and optional AES-256 encryption
- **Post announcements** that show up on every student's dashboard
- **Broadcast push notifications** to all users instantly (or filter by tags)
- **Tag students** into groups like "Batch A" or "Group 3" for targeted messages
- **Support inbox** — read and respond to student messages (responses sent via Resend API)
- **Analytics** — charts showing user growth, logins, and activity over time
- **Health monitor** — database stats, service status, cleanup jobs
- **Security dashboard (SOC)** — live feed of blocked IPs, suspicious requests, and every audit log entry
- **Emergency lockdown** — one button that logs everyone out and freezes the site instantly
- **Cloudinary storage** — browse files on the CDN, scan for orphans, delete junk
- **Maintenance mode** — shows a maintenance screen to students while you work

</details>

---

## 🏗️ Architecture

Here's how everything is connected:

```
┌────────────────────────────────────────────────────────┐
│                  USER'S DEVICE                         │
│          Android APK  /  iPhone PWA                    │
└───────────────────────┬────────────────────────────────┘
                        │  HTTPS
                        ▼
┌────────────────────────────────────────────────────────┐
│             VERCEL  (Next.js 15 PWA)                   │
│                                                        │
│   ┌─────────────────┐      ┌──────────────────────┐   │
│   │  Edge Middleware │      │   App Router + SWR   │   │
│   │  (blocks probes) │      │  (pages + caching)   │   │
│   └─────────────────┘      └──────────────────────┘   │
└──────────────────────────┬─────────────────────────────┘
                           │  REST API + WebSocket
                           ▼
┌────────────────────────────────────────────────────────┐
│             RENDER  (Flask 3.1 + Gunicorn)             │
│                                                        │
│  ┌────────────┐  ┌──────────┐  ┌──────────────────┐  │
│  │ Flask Login│  │SocketIO  │  │  Thread Pool     │  │
│  │  Sessions  │  │Real-time │  │  (async logging) │  │
│  └────────────┘  └──────────┘  └──────────────────┘  │
│  ┌────────────┐  ┌──────────┐  ┌──────────────────┐  │
│  │Rate Limiter│  │ Talisman │  │  IDS Scanner     │  │
│  │  (Redis)   │  │ CSP/HSTS │  │  (blocks attacks)│  │
│  └────────────┘  └──────────┘  └──────────────────┘  │
└──────┬──────────────────┬─────────────────────────────┘
       │                  │
  ┌────▼────┐   ┌─────────▼─────────┐   ┌────────────┐
  │Supabase │   │    Cloudinary     │   │  Firebase  │
  │PostgreSQL   │  (File Storage)   │   │  FCM Push  │
  │(Pooled) │   │  + CDN Delivery   │   │  + VAPID   │
  └─────────┘   └───────────────────┘   └────────────┘
```

**Why this setup?**

| Choice | Reason |
|---|---|
| Vercel edge blocks attacks | Hacker probes (`.env`, `wp-admin`, `.php`) never even reach my server |
| Gevent + single Gunicorn worker | Handles WebSockets and many connections on Render's free tier |
| Supavisor connection pooling | Multiplexes hundreds of app connections into one clean PostgreSQL stream |
| SWR on the frontend | Pages feel instant — data is cached and refreshed in the background |
| Capacitor for Android | Same web app, zero separate native codebase, signed APK on every release |

---

## 🔒 Security

Every layer of the app has security built in — not bolted on.

### Defense overview

| Layer | What it does |
|---|---|
| 🌐 **Edge firewall** | Blocks `.env`, `/wp-admin`, `.php`, SQL keywords before they hit Flask |
| 🚦 **Rate limiting** | Stops spam and brute force — `5/min` on login, `50/day` on registration |
| 🕵️ **Attack scanner (IDS)** | Scans every URL param and JSON body for SQLi, XSS, and path traversal |
| 🖥️ **Device binding** | Your session is tied to your OS + browser. Max 2 devices per account |
| 🍪 **Secure cookies** | `Secure; HttpOnly; SameSite=None` — can't be stolen by JavaScript |
| 🔐 **AES-256 file encryption** | Files flagged by admins are encrypted before going to Cloudinary |
| 🖼️ **Secure canvas viewer** | Encrypted files render in a canvas — no download, no print, no screenshot copy |
| 📜 **Audit logs** | Every login, approval, upload, and admin action is saved permanently |
| 🔴 **Emergency lockdown** | One button that wipes all sessions and freezes the site instantly |
| 📧 **Email Verification** | Secure OTPs for registration and password resets delivered via SMTP |
| 🔔 **Push auth** | Device tokens only bind to accounts after login — pending users get approval alerts only |

### How registration works

```
Student fills in the form
        ↓
Account is created in "pending" state
        ↓
Admin sees them in the pending queue
        ↓
Admin clicks Approve or Reject
        ↓
Student gets a push notification immediately
        ↓
If approved → full access to the portal
If rejected → locked out with a reason shown
```

No student can see any content until a human admin has manually reviewed their account.

---

## 📂 Folder Structure

```
StudentWebsite/
│
├── 📁 backend/                    ← Python / Flask API
│   ├── portal/
│   │   ├── __init__.py            ← app factory, auto seeds admin on boot
│   │   ├── config.py              ← dev vs production settings
│   │   ├── models/
│   │   │   └── models.py          ← all database table definitions
│   │   ├── routes/
│   │   │   ├── auth.py            ← login, register, logout, session purge
│   │   │   ├── admin.py           ← all admin actions (CRUD, broadcast, SOC)
│   │   │   ├── main.py            ← student-facing routes
│   │   │   ├── push.py            ← Web Push + FCM token registration
│   │   │   └── analytics.py      ← security stats for the SOC
│   │   ├── utils/
│   │   │   ├── notifications.py   ← sends push to browsers and Android
│   │   │   ├── watermark.py       ← stamps PDFs and images with branding
│   │   │   ├── security.py        ← IDS, hCaptcha, device fingerprinting
│   │   │   ├── storage.py         ← Cloudinary upload/delete helpers
│   │   │   └── passwords.py       ← bcrypt hashing
│   │   └── sockets/
│   │       └── events.py          ← WebSocket event handlers
│   ├── app.py                     ← Gunicorn entry point
│   └── requirements.txt
│
├── 📁 frontend/                   ← Next.js 15 web app
│   ├── src/
│   │   ├── app/                   ← all pages (App Router)
│   │   │   ├── admin/             ← 20 admin panel pages
│   │   │   ├── dashboard/         ← student home
│   │   │   ├── subjects/          ← subject browser
│   │   │   ├── assignments/       ← file viewer
│   │   │   ├── notifications/     ← notification center
│   │   │   ├── profile/           ← account + device manager
│   │   │   ├── pending/           ← approval waiting room
│   │   │   ├── login/             ← sign in
│   │   │   └── register/          ← sign up
│   │   ├── components/
│   │   │   ├── AppShell.tsx       ← main layout + nav bar
│   │   │   ├── AdminShell.tsx     ← admin panel layout
│   │   │   ├── SecureCanvasViewer ← renders encrypted files safely
│   │   │   ├── PushNotificationManager ← sets up browser push
│   │   │   ├── PWAInstallPrompt   ← "install the app" bottom banner
│   │   │   ├── AppUpdater         ← detects and prompts for new versions
│   │   │   └── SuccessModal       ← clean success/error popups
│   │   └── hooks/
│   │       └── useNativeApp.ts    ← Capacitor push + privacy screen
│   └── android/                   ← Capacitor Android project (auto-generated)
│
├── 📁 .github/
│   └── workflows/
│       └── build-release.yml      ← CI/CD: builds signed APK on every version tag
│
├── release.js                     ← one-command release script
└── render.yaml                    ← Render deployment config
```

---

## 🗄️ Database Tables

| Table | What it stores |
|---|---|
| `users` | Every account — students, instructors, admins, owners |
| `subjects` | Courses linked to semesters |
| `semesters` | Academic terms (Spring 2025, Fall 2025, etc.) |
| `assignments` | Files per subject, with Cloudinary URL and encryption flag |
| `announcements` | School-wide news posts |
| `notifications` | Per-user alerts with read/unread status |
| `notification_events` | Broadcast events — one event, one row per student |
| `push_subscriptions` | Device tokens for Web Push and FCM |
| `user_devices` | OS + browser fingerprint per session |
| `security_logs` | Full audit trail — every login, approval, admin action |
| `system_settings` | Key-value config (maintenance mode, lockdown, etc.) |
| `enrollments` | Which student is in which subject (many-to-many) |
| `ratings` | Student ratings on subjects |
| `route_maps` | Admin-managed navigation links |
| `system_tags` | Admin-created tags for grouping students |
| `download_logs` | Tracks who downloaded which file and when |
| `notification_status` | Tracks delivery and read state for broadcasts |

---


## 🕵️ Detailed Project Analysis & Engineering Mechanics

### 🛡️ Multi-Layer Security Architecture
The platform implements defense-in-depth security to protect academic materials, user privacy, and prevent unauthorized scraping:
- **Intrusion Detection System (IDS):** Backend middleware automatically scans every request (URL parameters, JSON payloads, headers) for malicious patterns such as SQL injection (SQLi) and cross-site scripting (XSS). Offenders are automatically IP-blocked and reported in real-time to the admin SOC (Security Operations Center) dashboard.
- **Frontend Security Enforcer:** A strict client-side lock that acts as an anti-scraping measure. It actively detects DevTools desyncs, intercepts screenshot shortcuts (Print Screen, Windows Snipping Tool, Mac shortcuts), and disables context menus. Focus loss on secure routes immediately triggers a blurry lock screen to prevent background recording software from capturing content.
- **System Guard (Global Gateway):** A centralized layout wrapper that intercepts every route transition in Next.js. It validates session state, device linkage, role-based access control (RBAC), and checks for global maintenance/lockdown flags before rendering the requested page.
- **Device Fingerprinting:** Every login generates a unique hardware and browser fingerprint. User sessions are strictly tied to this identity, and the system enforces a strict device limit per account (e.g., maximum 2 devices). Suspicious login locations or third-party devices are rejected, and admins can selectively deregister individual devices.

### 🔄 Hybrid Over-The-Air (OTA) Updates
To bypass the friction of app store review cycles and ensure all students are running the latest version, the app utilizes a custom OTA update mechanism:
- **Web App Delivery:** Instantly updated globally via standard Vercel edge deployments.
- **Android APK Engine:** The custom `AppUpdater` component polls the backend for version manifests. If a hot-update is available, it pulls the new bundle directly into the app's writable `data/` directory. When the user clears their cache or data, the app gracefully falls back to the read-only APK base bundle, preventing bricking.
- **Zero-Downtime Webhooks:** Deployment pipelines trigger an authenticated webhook to place the system into "Maintenance Mode", alerting active users with visible push alerts, then automatically pulling them back online when the deployment completes.

### 📡 Multi-Channel Push Notification Delivery
Notifications are delivered reliably across all devices to keep students informed of critical academic updates:
- **Web Browsers (VAPID):** Handled via `pywebpush`, allowing delivery of native browser notifications on Windows, macOS, and standard mobile browsers without needing a native app.
- **Android (FCM):** Delivered securely using `firebase-admin` via Google's Firebase Cloud Messaging, ensuring delivery even when the app is completely closed or running in the background.
- **Background Dispatchers:** Both notification streams are managed in parallel by background worker tasks on the Flask backend, ensuring instant, non-blocking delivery to hundreds of students simultaneously.

### ⚡ Real-Time Socket Architecture
The platform leverages WebSockets to keep the UI perfectly synchronized without requiring manual refreshes:
- **Live SOC Feed:** Admins watching the Security Operations Center page see threat blocks and IP bans populate instantly via dedicated socket namespaces.
- **Dynamic Presence:** The directory page utilizes active socket connections to determine which students are currently online, displaying a live green indicator next to active users.
- **Instant State Invalidation:** When a student is approved or a new announcement is posted, the backend emits a socket event that instructs connected clients to invalidate their SWR cache, causing the UI to seamlessly fetch the new data in the background.

---

## 🧰 Libraries Used

<details>
<summary><b>Backend (Python)</b></summary>

<br>

| Library | What it does |
|---|---|
| **Flask** | The web framework everything runs on |
| **Flask-SQLAlchemy** | Connects Python objects to the database |
| **Flask-Migrate** | Handles database schema changes safely |
| **Flask-Login** | Keeps track of who is logged in |
| **Flask-SocketIO** | Real-time WebSockets for live notifications and the SOC feed |
| **Flask-Limiter + Redis** | Rate limiting — stops brute force and spam |
| **Flask-Talisman** | CSP headers, HSTS, and other security headers |
| **Flask-CORS** | Lets the frontend talk to the backend across domains |
| **pywebpush** | Sends Web Push notifications to browsers |
| **firebase-admin** | Sends FCM push to Android devices |
| **Pillow** | Image watermarking |
| **pypdf** | PDF watermarking |
| **python-docx** | Word document watermarking |
| **cryptography** | AES-256-GCM encryption for protected files |
| **cloudinary** | Uploading and managing files on the CDN |
| **bcrypt** | Hashes passwords before storing them |
| **tenacity** | Retries failed operations automatically |
| **bleach** | Strips dangerous HTML to prevent XSS |
| **resend** | Sending support emails to admins and students |
| **smtplib** | Built-in Python library for sending OTPs and password resets via SMTP |
| **SQLAlchemy** | Core SQL toolkit and ORM |
| **Flask-Cors** | Handles Cross-Origin Resource Sharing |
| **gevent + psycogreen** | Async I/O for handling many connections at once |

</details>

<details>
<summary><b>Frontend (JavaScript)</b></summary>

<br>

| Library | What it does |
|---|---|
| **Next.js 15** | The React framework — routing, SSR, static export |
| **TypeScript** | Type safety so bugs are caught before they reach production |
| **SWR** | Smart data fetching with background refresh and caching |
| **Socket.IO client** | Connects to the backend WebSocket for real-time updates |
| **Capacitor** | Wraps the web app into a native Android APK |
| **Custom sw.js** | Native service worker for aggressive offline caching and PWA installs |
| **hCaptcha** | Privacy-respecting bot protection on auth routes |
| **Lucide React** | Clean and consistent icon set |
| **Recharts** | Charts used in analytics and the SOC page |
| **Sonner** | Toast/popup notifications |
| **Framer Motion** | Complex UI animations |
| **React Hook Form + Zod** | Form validation and state management |
| **Capacitor Updater** | Handles Over-The-Air (OTA) updates |

</details>

---

## 🏗️ How a Request Works

**Normal page:**
```
You open a page
  → Vercel edge checks for attack signatures (blocks bad ones instantly)
  → Next.js loads in the app shell
  → SWR fetches data from Flask on Render
  → Flask checks your session cookie, rate-limits the IP, scans the request
  → Queries PostgreSQL through Supavisor
  → Data comes back → page renders
```

**Real-time update (notification, SOC feed):**
```
You're on a page → Socket.IO connects in the background
  → Something happens server-side (approval, new post, blocked IP)
  → Flask emits a socket event
  → The app receives it and updates the UI — no page refresh needed
```

---

## 📋 All Admin Pages

| # | Page | What you can do |
|---|---|---|
| 1 | **Dashboard** | Overview — pending users, total counts, recent activity |
| 2 | **Users** | Browse all accounts, search, filter by role or status |
| 3 | **User Editor** | Edit role, permissions, suspend, revoke specific devices |
| 4 | **Subjects** | Create and manage all subjects |
| 5 | **Subject Editor** | Edit subject name and linked semester |
| 6 | **Semesters** | Create, activate, and archive academic terms |
| 7 | **Semester Editor** | Rename an existing semester |
| 8 | **Announcements** | Post news visible to all students |
| 9 | **Announcement Editor** | Edit an existing post |
| 10 | **Broadcast** | Send a push notification to every user right now |
| 11 | **Upload Vault** | Upload assignments with watermark/encryption options |
| 12 | **Tags** | Create labels to group students for targeted broadcasts |
| 13 | **Analytics** | Signup trends, login counts, active user charts |
| 14 | **SOC** | Live security threat feed — blocked IPs, suspicious requests |
| 15 | **Security** | Full audit log, IP blocklist, lockdown toggle |
| 16 | **Health** | Database size, service status, cleanup tools |
| 17 | **Storage** | Cloudinary usage, orphan file scanner, purge tools |
| 18 | **Settings** | General system configuration |
| 19 | **Vault** | Encrypted file manager |

---

## 🤔 FAQ

<details>
<summary><b>Why do accounts need manual approval?</b></summary>
So random people can't just sign up and start downloading school materials. Every new account gets reviewed by an admin before anything unlocks. The student gets a push notification the moment they're approved or rejected.
</details>

<details>
<summary><b>Can many students use it at the same time?</b></summary>
Yes. The backend uses Gevent which handles hundreds of concurrent connections in a single process. Database connections are pooled through Supavisor so PostgreSQL doesn't get overwhelmed.
</details>

<details>
<summary><b>What about Render's free tier cold starts?</b></summary>
The first request after the server idles will take a few seconds. You can use <a href="https://uptimerobot.com">UptimeRobot</a> to ping <code>/ping</code> every 10 minutes to keep it alive.
</details>

<details>
<summary><b>Is the encrypted file stored in plain text anywhere?</b></summary>
No. Encryption happens on the server before the file ever reaches Cloudinary. Cloudinary only stores the scrambled version. The decryption key lives in the PostgreSQL database — not on the CDN.
</details>

<details>
<summary><b>Can a student share their account?</b></summary>
Not really. Only 2 devices can be logged in at a time. A 3rd login is blocked. Admins can also view and remove individual devices from the User Editor page.
</details>

<details>
<summary><b>Does it work offline?</b></summary>
The app shell and previously visited pages load from the service worker cache. Anything that needs live data (downloading files, seeing new notifications) still needs an internet connection.
</details>

---

## 👨‍💻 Developer

<div align="center">

<br>

**Rochak Agarwal**

Built this from scratch — backend, frontend, Android packaging, CI/CD pipeline, security, the whole thing.

[![GitHub](https://img.shields.io/badge/GitHub-R--git--ui-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/R-git-ui)

<br>

</div>

---

## 📄 License

This project's source code is **private** for security reasons. The README is public so people can understand what the project does and how it's built, but the actual codebase is not available publicly.

---

<div align="center">

<br>

*If this helped you, drop a ⭐ on the repo — it genuinely means a lot.*

<br>

</div>
