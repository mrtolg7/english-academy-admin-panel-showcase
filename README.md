# English Academy — Admin Panel

> **Showcase repository.** The source code of this project is private because it is in production use by a real language school. This repository contains only documentation and screenshots. Code walkthroughs are available on request.

A single-tenant management panel built for a language school. It replaces spreadsheets for tracking students, teachers, class groups, attendance and tuition payments, and it runs in production as a single Node.js service on Railway.

All screenshots use **fictional demo data**. None of the names, ID numbers or phone numbers belong to real people.

![Dashboard](screenshots/01-dashboard.png)

## Features

- **Students, teachers and groups.** Full CRUD with many-to-many group assignment, search and group filters, plus a styled Excel (XLSX) export of the student list.
- **Attendance.** Per-group, per-day roll call (present / absent / excused). The system can generate an official-style attendance sheet in XLSX for any date range of up to one year.
- **Accounting.**
  - Plans can be paid up front or in installments.
  - Partial payments are supported.
  - Overdue installments are tracked.
  - A monthly revenue chart is included.
  - Each plan can be exported to Excel.
  - Balances are never stored. They are always computed from the payment records, so there is no stale data.
- **Two-layer security for finances.** The accounting pages sit behind a second password gate with its own short-lived token. This gate is separate from the main admin session and has its own inactivity timeout.
- **Session safety.** A 60-minute inactivity logout on the client is combined with an absolute JWT expiry on the server.
- **Notifications.** The panel raises notifications for new enrolments, expiring teacher contracts and overdue installments, and tracks which ones have been read.
- **Backup and restore from the browser.**
  - Portable, versioned JSON backups with a SHA-256 integrity check.
  - Restore runs in a single transaction and needs explicit confirmation plus the admin password.
  - This works on hosted Postgres without `pg_dump` or Docker.

## Tech stack

| Layer | Technology |
| --- | --- |
| Frontend | React 18, Vite, React Router, Tailwind CSS |
| Backend | Node.js, Express 5, JWT auth, rate limiting |
| Database | PostgreSQL with raw parameterized SQL (no ORM) and idempotent migrations |
| Reports | ExcelJS (server-side XLSX generation) |
| Deployment | Railway, with the API and the built SPA served by a single process |

## Architecture

```
Browser (React SPA)
   │  Authorization: Bearer <admin JWT>
   │  X-Accounting-Authorization: Bearer <accounting JWT>   (payments only)
   ▼
Express 5 ──► /api/students, /teachers, /groups, /attendance, /dashboard, ...
   │          /api/payment-plans/*   (admin auth + accounting auth)
   │          /api/backups/*         (admin auth + password + confirmation)
   ▼
PostgreSQL ── balances computed with SUM() at read time, multi-table writes in transactions
```

## Screenshots

| | |
| --- | --- |
| ![Login](screenshots/00-login.png) | ![Students](screenshots/02-ogrenciler.png) |
| ![Student detail](screenshots/03-ogrenci-detay.png) | ![Groups](screenshots/04-gruplar.png) |
| ![Teachers](screenshots/05-ogretmenler.png) | ![Attendance](screenshots/06-devamsizlik.png) |
| ![Accounting](screenshots/07-muhasebe.png) | ![Student payment plan](screenshots/08-ogrenci-muhasebe.png) |

---

© Mert Olgun. All rights reserved. The screenshots and documentation may not be reused without permission.
