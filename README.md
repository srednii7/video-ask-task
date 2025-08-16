# Middle Full-Stack engineer test assessment - Description of the BE + FE Application Architecture

## 1. What We’re Building

* **SDK** that apps drop in to catch errors and send them to us.
* **API** that receives those errors and stores them.
* **Dashboard** where developers search and see the errors.
* **Email alerts** when a critical error pops up.

---

## 2. Tech Stack&#x20;

| Part       | Tech                                                      | Why                                                     |
| ---------- | --------------------------------------------------------- | ------------------------------------------------------- |
| SDK        | **JavaScript snippet** published on npm                   | Works in browser & Node, zero config.                   |
| API        | **Node.js + Express** (REST)                              | Easy to read, tons of examples.                         |
| DB         | **PostgreSQL** (JSONB column)                             | One service, SQL + flexible JSON; cheap on any cloud.   |
| Dashboard  | **React + Vite**                                          | Fast dev server, deploy as static files.                |
| Email      | **SendGrid API**                                          | Quick to set up, free tier.                             |
| Deployment | **Docker Compose** on a small VM (e.g., DigitalOcean)\*\* | One‑command up; can move to Kubernetes later if needed. |

---

## 3. How Data Flows

1. App hits an error → SDK captures stack trace.
2. SDK batches errors and POSTs to `/api/logs`.
3. API validates request, adds tenant & timestamp, inserts into `errors` table.
4. API checks: if `level = critical`, trigger email via SendGrid.
5. Dashboard calls `/api/logs?search=…` to show data.

---

## 4. Key Decisions

* **REST over GraphQL** – fewer moving parts.
* **Single DB** – avoid extra queues until traffic demands it.
* **Batching in SDK** – keeps API lightweight without Kafka.
* **JSONB** – flexible schema, no migrations for every field.
* **Docker Compose** – easy local dev & prod parity.

---

## 5. Ops & Monitoring

* **Backups** – daily pg\_dump to cloud storage.
* **Uptime** – Health‑check endpoint + Pingdom.
* **Logs** – App logs to file; optional Logtail or Papertrail.

---

## 6. Questions for the Product Owner

1. **Traffic**: expected errors per minute now & in 1 year?
2. **Retention**: how long must we keep logs? 30, 90, 365 days?
3. **Auth**: do we need per‑user accounts or one team token is fine?
4. **Critical definition**: what counts as “critical” for alerts?
5. **Budget**: monthly ceiling for hosting/email?
6. **Deployment**: any preferred cloud or must be on‑prem?

---

*Prepared by Yehor – Full‑Stack JavaScript Developer*
