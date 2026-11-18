# CampusHub starter (9-service exam)

The application code in `app/` is complete and works. Do not change it. Your job is the Docker work.

## Service contract
| Service | Image or build | Port | Notes |
|---|---|---|---|
| gateway | nginx-unprivileged | 8080 | only port published to the host |
| frontend | `app/frontend` (multi-stage) | 8080 | static files from `npm run build` (`dist/`) |
| api | `app/api` | 3000 | `npm start` runs migration + seed, then the server |
| worker | same image as api | none | command `node src/worker.js` |
| db | postgres | 5432 | user, password, db name from env |
| redis | redis | 6379 | password required (`REDIS_PASSWORD`) |
| rabbitmq | rabbitmq | 5672 | user and password from env |
| minio | minio | 9000 | bucket `campushub` is created by the API |
| mailhog | mailhog | 1025 SMTP, 8025 UI | |

Gateway routes: `/` to frontend, `/api/` to api, `/storage/` to `minio:9000/campushub/`.
Environment variable names are in `.env.example`. Copy it to `.env` and keep `.env` out of git.

## How to run
1. `cp .env.example .env` and fill in the values
2. `docker compose up -d --build --wait`
3. Open http://localhost:8080
4. Run `grade.sh` to verify

## Services
gateway (nginx), frontend, api, worker, db (postgres), redis, rabbitmq, minio (silo fork), mailhog

## Design decisions
1. Three networks (frontend, backend, data), with data marked internal: the database and redis cannot be reached from the gateway or the outside, which limits the blast radius.
2. Only the gateway is published on all interfaces: a single entry point is easier to secure, and the admin UIs are bound to 127.0.0.1.
3. The worker reuses the API image: one build gives identical code for both services, and the worker simply overrides the command and disables the API healthcheck.
