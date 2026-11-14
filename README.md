# Alcovia Intervention System

A backend that watches students' daily progress and automatically flags those who fall behind. When a student fails a daily check-in, they are locked into an intervention, a mentor is notified through an n8n webhook, the mentor assigns remedial work, and the student is unlocked once it is done.

## How it works

1. A student submits a **daily check-in** with a quiz score and focus minutes.
2. **Pass** (quiz score > 7 and focus > 60 minutes): the student stays *On Track*.
3. **Fail**: an intervention is created, the student moves to *Needs Intervention*, and an n8n webhook notifies a mentor.
4. The mentor **assigns** the intervention; the student moves to *Remedial*.
5. The student **completes** it and is back *On Track*.

## API

| Method | Route | Body | Result |
|---|---|---|---|
| GET | `/` | | Health check |
| POST | `/daily-checkin` | `{ student_id, quiz_score, focus_minutes }` | `On Track` or `Pending Mentor Review` |
| POST | `/assign-intervention` | `{ student_id, intervention_id }` | Mentor approves; task assigned |
| POST | `/complete-intervention` | `{ student_id }` | `Unlocked. Back on Track!` |

## Tech stack

Node.js · Express 5 · PostgreSQL (`pg`) · n8n webhooks · axios

## Run locally

```bash
cd backend
npm install
npm run dev     # nodemon src/app.js, port 4000
```

Environment variables (`backend/.env`):

| Variable | Purpose |
|---|---|
| `DB_HOST`, `DB_PORT`, `DB_USER`, `DB_PASSWORD`, `DB_NAME` | PostgreSQL connection (SSL enabled) |
| `N8N_WEBHOOK_URL` | Optional: workflow that notifies mentors |
| `PORT` | Server port, default 4000 |

## Design docs

| Doc | What's inside |
|---|---|
| [High-Level Design](docs/hld.md) | Architecture, components, data flow, tech choices, deployment |
| [Low-Level Design](docs/lld.md) | Modules, data models, APIs and functions, key sequences |

## License

MIT. See [LICENSE](LICENSE).
