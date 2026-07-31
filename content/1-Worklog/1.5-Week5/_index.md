---
title: "Week 5 Worklog"
date: 2026-07-06
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

### Stage: Cloud Architecture Design (Week 4 - Week 5)

### Week 5 Objectives:
* Integrate Real-time Order Tracking via WebSockets / AWS API Gateway WebSocket.
* Build Canteen Live Dashboard to receive real-time order notifications pushed by AWS Lambda.
* Finalize overall Cloud Integration topology: EC2, S3, CloudFront CDN, and AWS RDS.

### Tasks to be carried out this week:
| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - Establish Client WebSocket connection to AWS API Gateway WebSocket <br> - Author `useWebSocket` Custom Hook with auto-reconnection logic (Exponential Backoff) on disconnects | 06/07/2026 | 06/07/2026 | <https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-websocket-api.html> |
| 3 | - Build Real-time Order Tracking UI: 4-step progress bar (`PENDING`, `PREPARING`, `READY`, `COMPLETED` matching `models/order.py`)| 07/07/2026 | 07/07/2026 | <https://nextjs.org/docs> |
| 4 | - Construct **Canteen Live Dashboard**: Listen to event pushes from **AWS Lambda**, updating order cards instantly without manual refreshes | 08/07/2026 | 08/07/2026 | <https://docs.aws.amazon.com/lambda/latest/dg/welcome.html> |
| 5 | - Integrate Audio Alerts & Web Notification API for Browser Push Notifications on new orders | 09/07/2026 | 09/07/2026 | <https://developer.mozilla.org/en-US/docs/Web/API/Notifications_API> |
| 6 | - Finalize Cloud distribution topology| 10/07/2026 | 10/07/2026 | <https://aws.amazon.com/cloudfront/> |

### Week 5 Achievements:
* Order Tracking and Canteen Dashboard responding instantly to status pushes from AWS Lambda.
* Integrated Audio Alerts & Browser Push Notifications for Canteen Admin.
* Finalized Frontend static deployment strategy.