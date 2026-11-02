# NextGen Intelligence — complete frontend + backend

## Project files
- `index.html` — GitHub Pages frontend (HTML, CSS and JavaScript in one file).
- `main.py` — FastAPI backend, registration/login, student records, officer decisions, admin monitoring, audit logs and concerns.
- `ml_service.py` — explainable duplicate/risk indicators. These are review signals, not proof of wrongdoing.
- `audit_service.py`, `concern_service.py` — audit and concern workflows.
- `requirements.txt` — Python dependencies.

## Important: GitHub Pages does not run Python
Upload `index.html` to the GitHub repository for GitHub Pages. Deploy the Python files to a Python host (for example Render or another FastAPI-compatible host). Then set `API_BASE` near the bottom of `index.html` to the HTTPS backend URL.

## Local setup
1. Install Python 3.10+.
2. In this folder, run `python -m venv .venv` and activate it.
3. Run `pip install -r requirements.txt`.
4. Set environment variables before starting the backend:
   - `JWT_SECRET` — long random private secret.
   - `DATABASE_URL` — for example `sqlite:///./app.db` for local testing.
   - `ALLOWED_ORIGINS` — exact frontend origin, e.g. `http://127.0.0.1:5500` locally.
   - `ADMIN_SETUP_KEY` — private key used only once to create the first admin.
5. Run `uvicorn main:app --reload`.
6. Open `http://127.0.0.1:8000/docs` to test the API.
7. For a local frontend preview, run `python -m http.server 5500` in this folder and open `http://127.0.0.1:5500`.

## Create the first admin account
Use `POST /auth/register-admin` from the backend API docs. Send JSON:
`{"username":"your_admin_username","password":"a-long-password","setup_key":"YOUR_PRIVATE_ADMIN_SETUP_KEY"}`.
Never put `ADMIN_SETUP_KEY`, `JWT_SECRET`, database credentials, or admin passwords in `index.html` or GitHub. After creating the admin, remove/rotate `ADMIN_SETUP_KEY` in the hosting environment and redeploy.

## Roles
- Public account registration creates Officer accounts only.
- Officers can add student records and set a record to `accepted` or `rejected`.
- Admins can view student records, statistics, graph links, audit logs, and review submitted concerns.
- Admins do not have the officer student-status controls.
- There are no seeded demo users or student records.

## API
- `POST /auth/register` — create Officer account.
- `POST /auth/login` — form-encoded username/password.
- `GET /auth/me` — current account.
- `POST /students` — Officer only.
- `GET /students` — authenticated.
- `PATCH /students/{student_id}/status` — Officer only; `accepted` or `rejected`.
- `GET /admin/stats`, `GET /graph`, `GET /admin/audit-logs` — Admin only.
- `POST /concerns` — authenticated concern submission.
- `GET /concerns`, `PATCH /concerns/{concern_id}/review` — Admin only.
- `GET /health` — health check.

## Existing database warning
This starter creates missing tables, but it does not automatically migrate an older database schema. Back up any existing database before testing. For a fresh deployment, use a new database or create a migration before connecting an existing production database.

## Deployment checklist
- Set `JWT_SECRET`, `ADMIN_SETUP_KEY`, `DATABASE_URL`, and `ALLOWED_ORIGINS` in the backend host's private environment settings.
- `ALLOWED_ORIGINS` must contain the exact GitHub Pages origin, such as `https://YOUR-USERNAME.github.io` (not the full repository path).
- Change `API_BASE` in `index.html` to the backend HTTPS base URL (no trailing slash).
- Deploy backend first, then commit/push `index.html` to GitHub.
- Do not store real student data in a public demo. Apply your institution's privacy and authorization rules.
