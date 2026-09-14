# ULAVAN – THE FOODMAKER

A responsive agricultural procurement-management application that helps farmers submit crop requests, receive scheduled slots, follow official procurement milestones, view payments, and raise complaints.

## Included demo

This repository currently ships as a no-build, browser-based functional demo. Open `index.html` in a modern browser. The UI uses browser `localStorage` so actions persist after refresh on the same device. Use your browser's site-data controls to reset demo data.

There is also an Express/PostgreSQL API foundation in `backend/server.js`. After installing Node.js 20+ and PostgreSQL, run `npm install`, configure `.env`, apply the schema, and run `npm start`. It serves the demo at `http://localhost:4000` and exposes the initial secure auth, centers, crops, farmer request, and officer official-status endpoints.

Demo credentials (these are dummy accounts only):

| Role | Email | Password |
|---|---|---|
| Farmer | farmer@ulavan.demo | Farmer@123 |
| Officer | officer@ulavan.demo | Officer@123 |
| Admin | admin@ulavan.demo | Admin@123 |

## Working workflows

- Farmer registration/sign-in, editable profile, crop management and unique `ULV-2026-xxxxx` request submission.
- Farmer request list, visual procurement timeline, schedule, notification, payment and complaint pages.
- Officer request review, official status/slot/quality/accepted-quantity updates, payment updates, complaint responses, and announcements.
- Admin users, centers, requests, reports overview, complaints, and announcements.
- Farmer-controlled data is separate from officer update controls in the user interface.

## Architecture for production

The recommended deployment is React + Tailwind for the client, an Express API with JWT authentication, and PostgreSQL. `database/schema.sql` provides the relational schema and foreign-key constraints for that deployment.

Suggested API modules:

```
backend/
  controllers/  auth, crops, requests, schedules, payments, complaints
  middleware/   authenticate, authorize, validate
  routes/       auth, farmer, officer, admin
  services/     notifications, procurement-id generation
  config/       database, environment
database/
  schema.sql
frontend/
  src/components, pages, services, context
```

## Database setup

1. Create a PostgreSQL database named `ulavan`.
2. Run `psql -d ulavan -f database/schema.sql`.
3. In an Express backend, use environment variables rather than source-controlled credentials:

```env
DATABASE_URL=postgresql://USER:PASSWORD@localhost:5432/ulavan
JWT_SECRET=replace-with-a-long-random-secret
PORT=4000
```

Passwords must be hashed with bcrypt/argon2 before insert. API middleware must require JWT authentication, ensure farmers only query their own `farmer_id`, and scope officers to their assigned `center_id`. Only officer/admin routes may change official statuses, procurement records, schedules, or payments.

## Status model

`Request Submitted → Under Verification → Approved → Scheduled → Farmer Arrived → Weighing → Quality Checking → Accepted → Procurement Completed → Payment Processing → Payment Completed`

Rejected requests terminate the processing path. Payment statuses are `Payment Pending`, `Payment Processing`, and `Payment Completed`.

## Future enhancements

SMS/WhatsApp notices, Tamil localization, voice assistance, PWA/offline support, maps, digital weighing integration, UPI payouts, government API integrations, weather/price insights, and analytics forecasting.
