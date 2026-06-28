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

### 🎓 A complete Student Portal built for Students — web, Android & iOS

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

[🌍 Platforms](#-platforms) · [✨ Features](#-features) · [🏗️ Architecture](#️-architecture) · [🔒 Security](#-security) · [📂 Structure](#-folder-structure) · [🗄️ Database](#️-database-tables) · [⚙️ Local Setup](#️-local-setup) · [☁️ Deploy](#️-deploying-live) · [📦 Releases](#-releasing-a-new-version) · [🧰 Libraries](#-libraries-used) · [🤔 FAQ](#-faq)

</div>

---

## 🌍 Platforms

Built once, runs everywhere — same codebase, three platforms.

| | Platform | How it works |
|---|---|---|
| 🌐 | **Website** | Hosted on Vercel. Works in any browser like a normal website. |
| 📱 | **Android App** | Packaged into a signed `.apk` using Capacitor. Auto-built on every release. |
| 🍎 | **iPhone / iPad** | Install via Safari → "Add to Home Screen". Runs as a PWA. |

---

## ✨ Features

<details>
<summary><b>👨‍🎓 What students can do &nbsp;(click to expand)</b></summary>

<br>

- **Register & wait for approval** — no one gets in without an admin saying yes first
- **Dashboard** — see your subjects, latest announcements, and how many unread notifications you have
- **Browse subjects** — see everything you're enrolled in for the current semester
- **Download materials** — access lecture notes, slides, and assignments uploaded by your instructor
- **Secure file viewer** — encrypted files open inside a protected in-browser viewer. No download button, no right-click, no Ctrl+P
- **Push notifications** — get notified on your phone or PC the moment you're approved, or when a new file or announcement goes up
- **Profile & devices** — update your name, see which devices are logged into your account right now
- **Support** — message the admin directly from inside the app
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
- **Support inbox** — read and respond to student messages
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
│   Browser  /  Android APK  /  iPhone PWA              │
└───────────────────────┬────────────────────────────────┘
                        │  HTTPS
                        ▼
┌────────────────────────────────────────────────────────┐
│             VERCEL  (Next.js 15 Frontend)              │
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

---

## ⚙️ Local Setup

You need **Python 3.10+**, **Node.js 18+**, and a **PostgreSQL** database.
Free PostgreSQL: [Supabase](https://supabase.com/) · Free Redis: [Upstash](https://upstash.com/)

### Step 1 — Clone

```bash
git clone https://github.com/R-git-ui/StudentPortal.git
cd StudentPortal
```

### Step 2 — Backend

```bash
cd backend

# create a virtual environment
python -m venv venv
venv\Scripts\activate       # Windows
source venv/bin/activate    # Mac / Linux

pip install -r requirements.txt
flask run
```

Create `backend/.env`:

```env
FLASK_ENV=development
SECRET_KEY=put-any-long-random-string-here
DATABASE_URL=postgresql://user:password@host:5432/yourdb

# ── Admin account (auto-created on first boot) ──────────────
ADMIN_USERNAME=admin
ADMIN_PASSWORD=yourpassword
ADMIN_EMAIL=admin@yourschool.com
ADMIN_NAME=Administrator

# ── Cloudinary ──────────────────────────────────────────────
CLOUDINARY_CLOUD_NAME=your_cloud
CLOUDINARY_API_KEY=your_key
CLOUDINARY_API_SECRET=your_secret

# ── Push notifications ──────────────────────────────────────
VAPID_PRIVATE_KEY=your_vapid_private_key
VAPID_PUBLIC_KEY=your_vapid_public_key
VAPID_CLAIM_EMAIL=mailto:admin@yourschool.com

# ── Redis (optional — falls back to memory if not set) ──────
RATELIMIT_STORAGE_URI=redis://localhost:6379

# ── CORS ────────────────────────────────────────────────────
ALLOWED_ORIGINS=http://localhost:3000
```

### Step 3 — Frontend

```bash
cd frontend
npm install
echo "NEXT_PUBLIC_API_URL=http://localhost:5000" > .env.local
npm run dev
```

Open `http://localhost:3000`. Keep the backend running in a separate terminal.

> **First login:** The admin account is created automatically on first boot using your `ADMIN_*` values from `.env`. Just log in with those credentials and you're in.

---

## ☁️ Deploying Live

### Backend → Render

1. Go to [render.com](https://render.com) → New Web Service → connect your repo
2. Set root directory: `backend`
3. Build command: `pip install -r requirements.txt`
4. Start command:
```
gunicorn --worker-class geventwebsocket.gunicorn.workers.GeventWebSocketWorker --workers 1 --worker-connections 1000 --timeout 120 --bind 0.0.0.0:$PORT app:app
```
5. Add all your `.env` variables in Render's **Environment** tab
6. Hit Deploy — admin account is created automatically on first boot ✅

### Frontend → Vercel

1. Go to [vercel.com](https://vercel.com) → New Project → import this repo
2. Root directory: `frontend`
3. Add env variable: `NEXT_PUBLIC_API_URL` = your Render backend URL
4. Deploy — every push to `main` auto-redeploys ✅

---

## 📦 Releasing a New Version

Everything is automated. Just run:

```bash
node release.js v1.2.0
```

That's it. Here's what happens automatically:

```
release.js runs
  ├─ Updates version.txt and .env.production
  ├─ Commits everything and pushes to main
  │     └─ Vercel detects the push → redeploys frontend automatically
  └─ Creates and pushes git tag "v1.2.0"
        └─ GitHub Actions detects the tag → starts the build pipeline
```

### GitHub Actions pipeline (`.github/workflows/build-release.yml`)

```
1. 🔒  Puts site into Maintenance Mode via webhook
2. 📦  npm ci — installs all frontend dependencies  
3. ⚡  Builds Next.js as a static export
4. ☕  Sets up Java 17 for Android toolchain
5. 🤖  npx cap sync android — copies web build into Capacitor
6. 🔑  Decodes .jks keystore from GitHub Secrets (base64)
7. 🔨  ./gradlew assembleRelease — builds the signed APK
8. 🏷️  Creates a GitHub Release with the APK attached
9. 🔓  Takes site out of Maintenance Mode
```

### GitHub Secrets needed

| Secret | What it is |
|---|---|
| `NEXT_PUBLIC_API_URL` | Your Render backend URL |
| `NEXT_PUBLIC_HCAPTCHA_SITE_KEY` | hCaptcha site key (optional) |
| `ANDROID_KEYSTORE_BASE64` | Your `.jks` keystore file as base64 |
| `ANDROID_KEYSTORE_ALIAS` | Alias inside the keystore |
| `ANDROID_KEYSTORE_PASSWORD` | Keystore password |
| `ANDROID_KEY_PASSWORD` | Key password |
| `PUBLIC_REPO_TOKEN` | GitHub token with write access to the downloads repo |
| `SYSTEM_WEBHOOK_SECRET` | Secret for the maintenance mode webhook |

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
| **Lucide React** | Clean and consistent icon set |
| **Recharts** | Charts used in analytics and the SOC page |
| **Sonner** | Toast/popup notifications |

</details>

---

## 🏗️ How a Request Works

**Normal page:**
```
You open a page
  → Vercel edge checks for attack signatures (blocks bad ones instantly)
  → Next.js loads in your browser
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
  → Your browser receives it and updates the UI — no page refresh needed
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
| 13 | **Messages** | Student support inbox |
| 14 | **Analytics** | Signup trends, login counts, active user charts |
| 15 | **SOC** | Live security threat feed — blocked IPs, suspicious requests |
| 16 | **Security** | Full audit log, IP blocklist, lockdown toggle |
| 17 | **Health** | Database size, service status, cleanup tools |
| 18 | **Storage** | Cloudinary usage, orphan file scanner, purge tools |
| 19 | **Settings** | General system configuration |
| 20 | **Vault** | Encrypted file manager |

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

<details>
<summary><b>How do I generate VAPID keys for push notifications?</b></summary>

```bash
python -c "from py_vapid import Vapid; v = Vapid(); v.generate_keys(); print('Private:', v.private_key); print('Public:', v.public_key)"
```
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
