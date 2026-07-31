---
title: "Week 4 Worklog"
date: 2026-06-29
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

### Stage: Cloud Architecture Design (Week 4 - Week 5)

### Week 4 Objectives:
* Contribute Frontend perspective to Cloud Architecture Proposal adhering to AWS Well-Architected Framework.
* Build Zustand Cart Store & Checkout Form connected to Order Placement API (`routers/orders.py`).
* Design VPC network topology and prepare AWS RDS PostgreSQL database integration.

### Tasks to be carried out this week:
| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - Author Frontend & UX sections in the team's **Cloud Architecture Proposal** <br> - Study the 6 pillars of AWS Well-Architected Framework (Operational Excellence, Security, Reliability, Performance, Cost, Sustainability) | 29/06/2026 | 29/06/2026 | <https://aws.amazon.com/architecture/well-architected/> |
| 3 | - Build **Zustand Cart Store**: Implement item CRUD logic and total calculation matching `OrderItemSchema` (`schemas/order.py`) <br> - Persist cart state to `localStorage` via Zustand persist middleware | 30/06/2026 | 30/06/2026 | <https://zustand-demo.pmnd.rs/> |
| 4 | - Develop Checkout Page UI: Delivery location input & automated VietQR API integration <br> - Validate inputs using React Hook Form + Zod Schema matching `OrderCreateSchema`| 01/07/2026 | 01/07/2026 | <https://react-hook-form.com/> |
| 5 | - Connect Order Placement API (`POST /orders/` from `routers/orders.py`)storing data into AWS RDS PostgreSQL <br> - Develop Order History UI (`GET /orders/`) using TanStack Query | 02/07/2026 | 02/07/2026 | <https://tanstack.com/query/latest> |
| 6 | - Review VPC design (Public/Private Subnets, NAT Gateway) ensuring secure API routing from Frontend to Backend and RDS | 03/07/2026 | 03/07/2026 | <https://aws.amazon.com/vpc/> |

### Week 4 Achievements:
* Contributed Frontend architecture sections to the AWS Well-Architected Cloud Proposal.
* Completed Zustand Cart Store managing global state with `localStorage` persistence.
* Connected Order Placement API (`POST /orders/`) and Order History API (`GET /orders/`) storing data in AWS RDS.
* Integrated VietQR automated payments and React Hook Form + Zod schema validation.