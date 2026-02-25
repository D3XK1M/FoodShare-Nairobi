# AGENTS.md

## Cursor Cloud specific instructions

### Project Overview

FoodShare Nairobi is a Node.js/Express monolith that serves both the REST API and static HTML frontend. It uses MySQL for persistence. There is no build step, no linter, and no test framework configured.

### Running the Application

- **Dev server**: `npm run dev` (uses nodemon for hot-reload)
- **Production**: `npm start`
- The server listens on port 3000 (configurable via `PORT` env var) and serves the `frontend/` directory as static files.

### Database

- MySQL must be running before starting the server. Start it with:
  ```
  sudo mysqld --user=mysql --datadir=/var/lib/mysql --socket=/var/run/mysqld/mysqld.sock --pid-file=/var/run/mysqld/mysqld.pid &
  ```
- The `.env` file at the project root must contain `DATABASE_HOST`, `DATABASE_USER`, `DATABASE_PASSWORD`, `DATABASE_NAME`, and optionally `DATABASE_PORT`.
- The database `foodshare_db` contains 13 tables: `donor`, `charity`, `admins`, `donations`, `food_donations`, `food_needs`, `donor_offers`, `charity_verifications`, `complaints`, `appeals`, `notifications`, `settings`, `feedback`.
- **Important**: The SQL files in `backend/Database/Database Schema/` are incomplete and do not match the actual schema the code expects. The full schema must be created manually (see the setup session or the SQL in the database setup step).

### Gotchas

- The `backend/db.js` pool uses `ssl: { rejectUnauthorized: false }` which is fine for local MySQL but emits a `DEP0123` deprecation warning. This is harmless.
- There is no automated test suite (`npm test` just exits with error). Validation must be done via API calls or browser testing.
- There is no ESLint or other linter configured.
- Admin passwords are stored in plaintext (not hashed with bcrypt like donor/charity passwords). The test admin account uses `password: 'admin123'` and `access_key: 'admin-key-123'`.
- Email features (password reset, charity approval notifications) require `EMAIL_USER` and `EMAIL_PASSWORD` env vars. Without them, the app runs fine but email sends fail silently.

### Key API Endpoints for Testing

- `GET /api/health` — health check
- `POST /signup/donor` — donor registration
- `POST /signup/charity` — charity registration
- `POST /auth/login` — login (requires `role` field: `donor`, `charity`, or `admin`)
- `GET /api/admin/dashboard-data` — admin dashboard stats
