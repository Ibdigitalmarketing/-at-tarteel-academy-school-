# AT-TARTEEL ACADEMY — School Management System

**Nurturing Knowledge, Building Character, Inspiring Excellence.**

This repository is a runnable TypeScript foundation for the AT-TARTEEL ACADEMY platform. It includes a Next.js App Router frontend and an Express/Mongoose API with authentication, role-based authorization, user provisioning rules, audit logging, validation, rate limiting, Swagger documentation, and a first dashboard.

> **Production status:** This is a Phase 1 foundation, not a finished production school platform. It does not yet implement every module in the master specification (CBT, LMS, payroll, fees/payment gateway, hostel, transport, library circulation, messaging, SMS/FCM, PDF reports, or full public school website). Do not use real student data or collect payments until those modules, security review, backups, monitoring, and deployment configuration are completed.

## Architecture

- `apps/web` — Next.js App Router, TypeScript, Tailwind CSS
- `apps/api` — Express, TypeScript, Mongoose, JWT access/refresh tokens
- MongoDB Atlas — database
- Access control — Super Admin, School Admin, Teacher, Parent, Student
- Audit events — successful user provisioning and sign-in events
- API docs — `/api/docs`

## Requirements

- Node.js 20.11+ (LTS recommended)
- npm 10+
- MongoDB local instance or MongoDB Atlas connection string

## Quick start

1. Copy `apps/api/.env.example` to `apps/api/.env` and set the secrets and MongoDB URI.
2. Copy `apps/web/.env.example` to `apps/web/.env.local`.
3. Install dependencies from the repository root:

   ```bash
   npm install
   ```

4. Start the API and frontend in separate terminals:

   ```bash
   npm run dev:api
   npm run dev:web
   ```

5. Open `http://localhost:3000`. API health: `http://localhost:4000/api/health`; Swagger: `http://localhost:4000/api/docs`.

## Initial Super Admin

For safety, there is no hard-coded default administrator. Create the first Super Admin using the one-time bootstrap endpoint:

`POST /api/auth/bootstrap-super-admin`

JSON body:

```json
{
  "bootstrapSecret": "the value of BOOTSTRAP_SECRET",
  "name": "Platform Owner",
  "email": "owner@example.com",
  "password": "Use-A-Long-Unique-Password-123!"
}
```

Set `BOOTSTRAP_SECRET` to a long random value before starting the API. After creating the first Super Admin, disable bootstrap by removing `BOOTSTRAP_SECRET` and restarting the API. The endpoint refuses to create another Super Admin if one already exists.

## Account creation policy

- Public self-registration is not provided.
- Only Super Admin can create School Admin accounts.
- Only School Admin can provision Teacher, Parent, and Student accounts.
- Every protected API route requires a valid access token and the appropriate role.
- Passwords are bcrypt-hashed. Refresh tokens are stored as hashes and rotated.

## API endpoints

- `POST /api/auth/login`
- `POST /api/auth/refresh`
- `POST /api/auth/logout`
- `POST /api/auth/bootstrap-super-admin` (one-time)
- `POST /api/users` (role-restricted provisioning)
- `GET /api/users` (Super Admin / School Admin)
- `GET /api/health`
- `GET /api/docs`

## Deployment outline

- Frontend: Vercel, root directory `apps/web`
- API: Railway or Render, root directory `apps/api`
- Database: MongoDB Atlas
- Configure environment variables in each host dashboard. Never commit `.env` files.
- Configure `CORS_ORIGIN` to the exact deployed frontend origin.
- Use HTTPS, managed secrets, MongoDB backups, log retention, monitoring, and a tested incident-response plan.

## Security checklist before real school use

- Rotate all secrets and use a secret manager.
- Add email verification, password reset, 2FA, CSRF strategy for cookie-based sessions, and account recovery.
- Add tenant/school scoping to all data models and queries before hosting multiple schools.
- Add field-level privacy controls and guardian/student relationship verification.
- Add malware scanning and strict MIME/content validation for uploaded files.
- Add audit coverage to every write operation, immutable log retention, backups, restore drills, and security testing.
- Obtain appropriate consent and comply with applicable Nigerian data protection requirements.
- Have the system independently security-reviewed.

## Folder structure

```text
apps/
  api/
    src/
      config/
      middleware/
      models/
      routes/
      services/
      utils/
  web/
    app/
    components/
    lib/
```
