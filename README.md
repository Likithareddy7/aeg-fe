# AegisQA frontend

The AegisQA web UI: Next.js 14 (App Router) + React. It talks to the AegisQA backend
(the separate `aegisqa-backend` repo) over REST and one WebSocket per project.

## Run locally

Prerequisites: Node.js 20+, and the backend running (by default on http://localhost:4000).

```bash
npm install
npm run dev          # http://localhost:3000
```

To point at a backend somewhere else, copy `.env.example` to `.env.local` and set
`NEXT_PUBLIC_API_BASE_URL` / `NEXT_PUBLIC_WS_BASE_URL`.

Other scripts:

```bash
npm run typecheck    # TypeScript check
npm run build        # production build
npm start            # serve the production build
```

## Deploy (Cloud Run)

```bash
docker build \
  --build-arg NEXT_PUBLIC_API_BASE_URL=https://<backend> \
  --build-arg NEXT_PUBLIC_WS_BASE_URL=wss://<backend> \
  -t aegisqa-frontend .
```

The image listens on port 3000. `NEXT_PUBLIC_*` values are baked in at build time, so
rebuild when the backend URL changes. The backend's `FRONTEND_ORIGIN` must be set to this
app's URL, or the browser's requests are refused. Next.js telemetry is disabled in the
image.

## Structure

```
app/          pages (App Router): login, projects, test cases, runs, reports, admin, help
components/   shared UI components
lib/          API client (lib/api.ts), WebSocket hook, config
shared/       API types -- the contract with the backend (imported as "@testingmcp/shared")
```

## Keeping in step with the backend

`shared/src/index.ts` describes every request and response the backend sends. When the
backend's API changes, update this file in the same release. The list of step actions
(`TEST_STEP_ACTIONS`) must match `STEP_ACTIONS` in the backend's
`aegisqa/agents/schemas.py`; the backend's `tests/test_schemas.py` checks this when both
repos are checked out side by side.
