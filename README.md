# HospitalConnect

Hospital onboarding / credentialing dashboard for the Telerad Group.
FastAPI backend + React/Vite frontend + MongoDB, deployed via Docker Compose behind nginx (HTTPS).

## Quick start

```bash
# 1. Configure
cp .env.example .env
nano .env                       # set SECRET_KEY, MS_CLIENT_ID, ALLOWED_ORIGINS

# 2. Prerequisites
docker network create prod_er_network    # if not already exists
# Place SSL cert + key at nginx/server.crt and nginx/server.key
# (or generate self-signed — see docs/DEPLOYMENT_GUIDE.md)

# 3. Build and start
docker compose up -d --build

# 4. Run migrations (idempotent, safe to re-run)
docker compose exec hospitalconnect-backend python migrations/add_qa_sa_countries.py
docker compose exec hospitalconnect-backend python migrations/migrate_cdf_to_gridfs.py
docker compose exec hospitalconnect-backend python migrations/drop_user_team_field.py
docker compose exec hospitalconnect-backend python migrations/add_can_confirm_stage_field.py
docker compose exec hospitalconnect-backend python migrations/run_once.py
```

Open `https://<your-host>:3007`.

## Repository layout

```
.
├── .env.example              Single .env template (copy → .env)
├── docker-compose.yml        Two-service compose (backend + frontend)
├── README.md                 This file
├── backend/
│   ├── Dockerfile
│   ├── main.py               Single-file FastAPI application
│   ├── requirements.txt
│   └── migrations/           Idempotent DB migrations (safe to re-run)
├── frontend/
│   ├── Dockerfile            Multi-stage: vite build → nginx
│   ├── package.json
│   └── src/                  React + TypeScript
├── nginx/
│   └── default.conf          Terminates HTTPS, proxies /api/* → backend
├── scripts/                  Standalone scripts (seed, ad-hoc migrations)
└── docs/                     Full documentation set
```

## Documentation

Pick the doc that matches what you need to do:

| Need | Doc |
|---|---|
| Get a one-page overview | `docs/QUICK_REFERENCE.md` |
| Use the app day-to-day | `docs/USER_GUIDE.md` |
| Add a user, run admin tasks | `docs/ADMIN_GUIDE.md` |
| Understand permissions | `docs/ACCESS_CONTROL.md` |
| Deploy or upgrade | `docs/DEPLOYMENT_GUIDE.md` |
| Backend endpoints | `docs/API_REFERENCE.md` |
| MongoDB schema | `docs/DATA_MODEL.md` |
| Debug a problem | `docs/TROUBLESHOOTING.md` |
| Test before release | `docs/TEST_CASES.md` |
| See what changed | `docs/RELEASE_NOTES.md` |

## License

Proprietary — Telerad Group.
