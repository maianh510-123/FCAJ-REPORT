---
title: "Week 3 Worklog"
date: 2026-06-22
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

### Stage: App Stabilization (Week 3)

### Week 3 Objectives:
* Containerize local Frontend/Backend development environment using Docker & Docker Compose.
* Program core views and connect RESTful APIs (`routers/auth.py`, `routers/menu.py`)with PostgreSQL.
* Test user authorization flows (Role Flow: Customer vs Canteen Admin) and execute.

### Tasks to be carried out this week:
| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - Write optimized Multi-stage `Dockerfile` for Next.js Frontend <br> - Configure `docker-compose.yml` orchestrating Frontend, FastAPI Backend, and PostgreSQL Database locally | 22/06/2026 | 22/06/2026 | <https://docs.docker.com/> |
| 3 | - Develop Login/Register views, connect Auth APIs (`POST /auth/login`, `POST /auth/register` from `routers/auth.py`) <br> - Store JWT Tokens securely and configure Axios Interceptors for Auth Headers | 23/06/2026 | 23/06/2026 | <https://nextjs.org/docs> |
| 4 | - Build Home & Menu Page UI, connect Menu API (`GET /menu/items` from `routers/menu.py`) <br> - Integrate Debounced Search Input for smooth searching without API spamming | 24/06/2026 | 24/06/2026 | <https://react.dev/> |
| 5 | - Develop Canteen Admin Menu Management UI: Connect Item CRUD APIs (`POST/PUT/DELETE /menu/items`) <br> - Test **Role Flow**: Ensure accurate navigation between Customer and Canteen Admin roles | 25/06/2026 | 25/06/2026 | <https://nextjs.org/docs> |
| 6 | - Conduct: Verify complete app workflow via Docker Compose | 26/06/2026 | 26/06/2026 | <https://docs.docker.com/> |

### Week 3 Achievements:
* Containerized Next.js Frontend using Docker, running smoothly with Backend & PostgreSQL via Docker Compose.
* Connected Auth APIs (`routers/auth.py`), managing JWT Token storage securely.
* Connected Menu APIs (`routers/menu.py`) with debounced searching capabilities.
* Completed Canteen Admin Menu view and verified Role Flow authorization permissions.
* Successfully performed.