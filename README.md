# Campus Digital Complaint Management System

Full-stack web application to manage student complaints with role-based access (Student, Admin, Super Admin), structured complaint lifecycle, SLA tracking, audit logs, proof-based closure (photo/video upload), notifications (SSE), and analytics.

## Tech stack

- Backend: Spring Boot (layered architecture: Controller → Service → Repository)
- Database: MySQL
- Security: Spring Security + JWT stored in **HttpOnly cookie**
- Frontend: React (Vite)

## Quick start (local, no Docker)

### 1) Start MySQL server (local install)

Make sure your local MySQL server is running on `127.0.0.1:3306`.

The backend uses these defaults from `backend/src/main/resources/application.properties`:

- URL: `jdbc:mysql://127.0.0.1:3306/campus_complaints?createDatabaseIfNotExist=true&useSSL=false&allowPublicKeyRetrieval=true`
- Username: `root`
- Password: `Shashi@8195`

### 2) Run backend

Use:

`powershell -ExecutionPolicy Bypass -File .\scripts\start-backend.ps1`

Backend runs on `http://localhost:8080`.

Default bootstrap Super Admin (configurable via env vars):

- Email: `superadmin@campus.local`
- Password: `superadmin123`

### 3) Run frontend

From `frontend/`:

`npm install`

`npm run dev`

Frontend runs on `http://localhost:5173` and proxies `/api/*` to the backend.

## Roles & workflows

- **Student**
  - Register/login
  - Submit complaint (title, category/domain, description, priority, image/video evidence)
  - View complaints and status
  - View lifecycle/audit history
  - Add comments and extra evidence after submission
  - View proof once uploaded by admin
  - Rate resolved complaints with feedback

- **Admin**
  - View all complaints + filters
  - Update status through lifecycle
  - Must provide proof when marking `RESOLVED` / `CLOSED` (if no proof exists yet)
  - Remarks recorded in audit log
  - Comment with students on complaint threads
  - Overdue complaints highlighted via API

- **Super Admin**
  - Create/enable/disable/delete Admin accounts
  - Manage complaint domains
  - Monitor all complaints from a dedicated complaints board
  - Assign complaints to admins
  - Set complaint deadlines and warn admins
  - View analytics dashboard with admin performance

## Email notifications

In-app notifications still work by default. To enable email delivery too, set:

- `EMAIL_NOTIFICATIONS_ENABLED=true`
- `EMAIL_NOTIFICATIONS_FROM=<sender-email>`
- `SMTP_HOST=<smtp-host>`
- `SMTP_PORT=<smtp-port>`
- `SMTP_USERNAME=<smtp-username>`
- `SMTP_PASSWORD=<smtp-password>`
- `FRONTEND_BASE_URL=http://localhost:5173`

### Gmail setup

For Gmail, use:

- `SMTP_HOST=smtp.gmail.com`
- `SMTP_PORT=587`
- `SMTP_USERNAME=<your-gmail-address>`
- `SMTP_PASSWORD=<your-gmail-app-password>`

To keep secrets out of git:

1. Copy `scripts/start-backend.local.example.ps1` to `scripts/start-backend.local.ps1`
2. Fill in your Gmail address and **App Password**
3. Start the backend with `powershell -ExecutionPolicy Bypass -File .\scripts\start-backend.ps1`

## Complaint lifecycle

`NEW → IN_PROGRESS → ON_HOLD → RESOLVED → CLOSED`

Allowed transitions are enforced in the backend.

## Department-scoped admins

Admins are created with a **department** (mapped to complaint `category`, e.g. `Hostel`, `University`). They can only view/update complaints in their own category; Super Admin can access all categories.

## API testing

Import the Postman collection in `postman/Campus-Complaint.postman_collection.json`.
