---
title: "Week 6 Worklog"
date: 2026-07-13
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

### Stage: Deployment Preparation (Week 6 - Week 7)

### Week 6 Objectives:
* Prepare automated Frontend deployment scripts (Build scripts, Nginx config, Dockerfile).
* Resolve Cross-Origin Resource Sharing (CORS) and Mixed Content (HTTP/HTTPS) issues.
* Upload images.

### Tasks to be carried out this week:
| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - Prepare Next.js static build scripts (`next build && next export`) and Nginx Server Block configs for hosting | 13/07/2026 | 13/07/2026 | <https://nextjs.org/docs> |
| 3 | - Resolve **CORS (Cross-Origin Resource Sharing)** errors: Configure Allowed Origins in FastAPI Backend & CloudFront Header Policies | 14/07/2026 | 14/07/2026 | <https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS> |
| 4 | - Eliminate **Mixed Content** errors: Ensure 100% of API requests and image URLs use HTTPS via AWS Certificate Manager (ACM) | 15/07/2026 | 15/07/2026 |  |
| 5 | - Integrate Image Upload: Fetch Presigned URL from Backend and upload images directly via HTTP `PUT` requests | 16/07/2026 | 16/07/2026 | <https://docs.aws.amazon.com/AmazonS3/latest/userguide/PresignedUrlUploadObject.html> |
| 6 | - Optimize image delivery using `next/image` and test image serving | 17/07/2026 | 17/07/2026 | <https://nextjs.org/docs/api-reference/next/image> |

### Week 6 Achievements:
* Prepared deployment scripts and Nginx server configs ready for Cloud deployment.
* Completely eliminated CORS issues between Frontend domain and Backend API domain.
* Resolved all Mixed Content errors, ensuring secure HTTPS communication across all endpoints.
* Integrated direct image uploading via Presigned URLs, offloading server bandwidth.