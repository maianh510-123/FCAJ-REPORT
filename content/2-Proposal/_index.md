---
title: "Proposal"
date: 2026-07-31
weight: 2
chapter: false
pre: " <b> 2. </b> "
includeInReport: false
---
# QuickBite on AWS Project Proposal

## 1. Project overview

QuickBite is a canteen ordering and operations platform for four user groups: customers, administrators, kitchen staff, and delivery staff. It supports menu browsing, order creation and tracking, item management, role-based order processing, revenue reports, audit logs, and menu-image uploads.

QuickBite initially ran locally with **React/Vite**, **FastAPI**, **PostgreSQL**, **Docker Compose**, and **Mailpit**. After completing the core business flows, I moved the system to AWS using a two-Availability-Zone high-availability architecture and managed the infrastructure as code with **Terraform**.

The proposal is not limited to hosting the application. It aims to deliver a reproducible, observable, cost-controlled environment aligned with the AWS Well-Architected Framework.

## 2. Background and problem statement

The local version met functional requirements but did not yet satisfy the operating requirements of a stable cloud environment:

1. Frontend, backend, and database responsibilities were not separated operationally.
2. The application had no HTTPS content-delivery layer.
3. The backend had no load balancing, Auto Scaling, or unhealthy-instance replacement.
4. The database had no synchronized standby across Availability Zones.
5. Menu images did not have dedicated object storage.
6. Secrets and permissions were not centrally managed.
7. Logs, metrics, alarms, and email notifications were not automated.
8. Infrastructure creation, modification, and deletion relied heavily on manual operations.
9. Cost drivers such as NAT Gateway, ALB, and RDS needed early visibility.
10. The system required deployment evidence rather than architecture diagrams alone.

## 3. Project scope

### 3.1. Functional scope

QuickBite includes the following core flows:

- Customers browse the menu, search for items, create orders, and track status.
- Administrators manage items, categories, orders, reports, and audit logs.
- Kitchen staff receive orders and update preparation status.
- Delivery staff receive and complete deliveries.
- The backend stores orders, statuses, operation history, and related data in PostgreSQL.
- Menu images are uploaded through the backend, stored in S3, and delivered through CloudFront.

### 3.2. Infrastructure scope

The infrastructure is deployed in **ap-southeast-1** and includes:

- Two Availability Zones.
- Public, private application, and isolated database subnets in each AZ.
- Application Load Balancer, EC2 Auto Scaling Group, and Multi-AZ RDS PostgreSQL.
- Two CloudFront distributions, private S3 buckets, ECR, Secrets Manager, Systems Manager, CloudWatch, and SNS.
- Terraform remote state in S3 with DynamoDB state locking.

### 3.3. Current exclusions

The following items are not treated as implemented production components in the current demo:

- Custom domain and Route 53.
- AWS WAF.
- A complete CI/CD pipeline.
- Controlled forced RDS failover and failure injection.
- Cross-Region backup strategy for production.

## 4. Objectives and success criteria

### 4.1. Functional objectives

- Preserve the complete customer → admin → kitchen → delivery flow.
- Provide dashboards, reports, audit logs, and order tracking.
- Upload menu images through the backend and deliver them through CloudFront.
- Expose a health endpoint for ALB and operational checks.
- Ensure the frontend can call the API over HTTPS without CORS or mixed-content failures.

### 4.2. Non-functional objectives

- Keep at least two EC2 instances active across two Availability Zones.
- Use ALB to route requests only to healthy targets.
- Run encrypted Multi-AZ RDS PostgreSQL.
- Manage EC2 through Systems Manager Session Manager without public SSH.
- Keep frontend and menu-image S3 buckets private and accessible through CloudFront OAC.
- Deliver logs and metrics to CloudWatch.
- Send alarm notifications through SNS email.
- Create and remove infrastructure through the Terraform lifecycle.

### 4.3. Acceptance criteria

The project is considered complete when:

- The frontend is available over HTTPS through CloudFront.
- The **/health** endpoint returns **ok**.
- Two EC2 instances are running and pass status checks.
- The ALB target group reports healthy targets.
- RDS operates in Multi-AZ mode.
- The backend pulls its image from ECR.
- Administrators can upload menu images and those images are stored and delivered correctly.
- The admin dashboard displays order and revenue data.
- CloudWatch records logs, metrics, and alarms.
- SNS sends email when an alarm changes state.
- Terraform plan shows no unexpected changes after deployment.

## 5. AWS services used

| Service | Deployed configuration | Role in QuickBite |
|---|---|---|
| Amazon CloudFront | 2 distributions | Delivers frontend, menu images, and API traffic over HTTPS |
| Amazon S3 | Web, menu-images, and tfstate buckets | Stores static assets, menu images, and Terraform remote state |
| Application Load Balancer | Internet-facing in 2 public subnets | Receives API traffic from CloudFront and routes it to EC2 |
| Amazon EC2 | 2 t3.micro instances in ap-southeast-1a and ap-southeast-1b | Runs the containerized FastAPI backend |
| Auto Scaling Group | min 2, desired 2, max 4 | Maintains capacity, replaces unhealthy instances, and supports scaling |
| Amazon RDS for PostgreSQL | db.t3.micro, Multi-AZ, encrypted | Stores business data with a synchronized standby |
| Amazon ECR | quickbite-backend repository | Stores versioned backend Docker images |
| AWS Secrets Manager | Shared application secret | Stores DATABASE_URL and JWT secret |
| AWS Systems Manager | Session Manager | Manages EC2 without public SSH |
| AWS IAM | EC2 instance role and service-linked roles | Grants least-privilege permissions |
| Amazon CloudWatch | Log group, CPU alarm, and target-tracking alarms | Collects logs, metrics, and operational signals |
| Amazon SNS | Email subscription | Sends alarm notifications |
| AWS Budgets | Budget alerts | Controls spending during deployment |
| AWS Cost Explorer | Cost analysis | Reviews usage by service and time |
| Amazon DynamoDB | Terraform state lock table | Prevents concurrent state updates |
| Internet Gateway and NAT Gateway | IGW for the public layer and NAT per AZ | Provides appropriate egress for public and private resources |

## 6. Architecture and component design
<img src="/images/2-Proposal/quickbite-aws-architecture-ha.png" alt="QuickBite high-availability AWS architecture" width="100%" />
<p><em>QuickBite deployed in ap-southeast-1 with Terraform, two Availability Zones, ALB, EC2 Auto Scaling, RDS Multi-AZ, CloudFront, S3, ECR, Secrets Manager, Systems Manager, CloudWatch, and SNS.</em></p>

### 6.1. Global edge and frontend

Users access QuickBite over HTTPS through CloudFront. The React frontend is built as static assets and stored in a private S3 bucket. CloudFront uses Origin Access Control to read S3 content without exposing the bucket publicly.

One CloudFront distribution serves both the frontend and menu images. The **/menu/** behavior routes menu-image traffic to the separate image bucket. This keeps storage responsibilities separate while preserving a single HTTPS delivery layer.

### 6.2. API and load balancing

The frontend calls the API through a separate CloudFront API distribution. This avoids a collision between the SPA route **/menu** and an API route with the same prefix.

CloudFront forwards API requests to the Application Load Balancer. The ALB runs across two public subnets and routes traffic to private EC2 instances on port 8000. Health checks ensure that traffic is sent only to working targets.

### 6.3. Application layer

FastAPI is packaged as a Docker image and stored in Amazon ECR. A Launch Template defines the initialization configuration for the Auto Scaling Group.

The Auto Scaling Group maintains at least two EC2 instances across two Availability Zones, with desired capacity 2 and maximum capacity 4. Instances do not need public IP addresses or public SSH access; administration is performed through Systems Manager Session Manager.

### 6.4. Data layer

RDS PostgreSQL runs in isolated database subnets. The primary and standby are placed in different Availability Zones and use synchronous replication.

The RDS Security Group accepts TCP 5432 only from the EC2 Security Group. The database is not directly accessible from the Internet or the ALB.

### 6.5. Storage and image uploads

Static frontend assets and menu images are stored in separate private S3 buckets. When an administrator uploads an image:

1. The frontend sends the file to the API.
2. FastAPI validates authorization and upload content.
3. The backend writes the object to the menu-images bucket.
4. The CloudFront URL is stored with the menu item.
5. Users retrieve the image through CloudFront instead of accessing S3 directly.

### 6.6. Security and secret management

DATABASE_URL and the JWT secret are stored in Secrets Manager. EC2 receives read access through its instance role.

The role is also limited to required operations: pulling images from ECR, reading the application secret, writing menu images to S3, sending logs to CloudWatch, and connecting through Systems Manager. This removes the need for long-lived access keys on EC2 or in the source code.

### 6.7. Monitoring and notification

Container stdout and stderr are delivered to CloudWatch Logs. CloudWatch monitors CPU and Auto Scaling-related metrics.

When an alarm changes state, SNS sends an email to the operator. This allows the system to be monitored without continuously opening the AWS Console.

## 7. Infrastructure as Code with Terraform

### 7.1. Bootstrap stack

The bootstrap stack uses local state to create:

- an encrypted and versioned S3 bucket for Terraform remote state;
- a DynamoDB table for state locking;
- an ECR repository for the backend image.

After bootstrap, the main stack uses remote state for safer and more consistent infrastructure management.

### 7.2. Main stack

The main stack is divided into three modules:

- **Network module:** VPC, two Availability Zones, public subnets, private application subnets, isolated database subnets, Internet Gateway, NAT Gateways, route tables, and Security Groups.
- **Data module:** Multi-AZ RDS PostgreSQL, DB subnet group, and Secrets Manager.
- **Application module:** S3, CloudFront OAC, two CloudFront distributions, ALB, Launch Template, Auto Scaling Group, IAM role, CloudWatch, SNS, and Systems Manager integration.

### 7.3. Benefits of Terraform

- Infrastructure is described as code and can be reviewed before apply.
- Terraform plan reveals changes before deployment.
- Remote state and state locking reduce concurrent-update risk.
- Modules separate network, data, and application responsibilities.
- Default tags help identify resources and analyze costs.
- Terraform destroy supports complete lifecycle cleanup.

## 8. Technical implementation

### 8.1. Deployment phases

| Phase | Main activities | Outcome |
|---|---|---|
| 1. Local review and stabilization | Review source, complete role flows, Docker Compose, and PostgreSQL | Stable end-to-end local baseline |
| 2. Cloud architecture design | Define VPC, subnets, ALB, ASG, Multi-AZ RDS, S3, CloudFront, and monitoring | Approved target architecture |
| 3. IaC preparation | Build bootstrap stack, remote state, ECR, and Terraform modules | Repeatable infrastructure |
| 4. Data and application deployment | Apply network/data/app, push Docker image, initialize database | Backend running on EC2 and connected to RDS |
| 5. Frontend deployment | Build React, sync to S3, configure CloudFront | Website available through HTTPS |
| 6. Functional validation | Test login, menu, order flow, dashboard, and image upload | Business flows working on AWS |
| 7. Monitoring and cost control | Configure CloudWatch Logs, alarms, SNS, Budgets, and Cost Explorer | Observability and spending alerts |
| 8. Report completion | Collect console screenshots, diagram, and test results | Complete deployment evidence |

### 8.2. Technical requirements

- Windows 11 and PowerShell as the working environment.
- AWS CLI v2 for authentication and resource verification.
- Terraform for infrastructure management.
- Docker Desktop for backend image builds and validation.
- Node.js and npm for the frontend build.
- IAM permissions for VPC, EC2, ALB, RDS, S3, CloudFront, ECR, IAM, Secrets Manager, SSM, CloudWatch, SNS, DynamoDB, and Budgets.
- Deployment Region: **ap-southeast-1**.
- The Docker image must be pushed to ECR before the Auto Scaling Group launches application instances.
- Database schema and seed data must be loaded before business-flow validation.

## 9. Roadmap and milestones

| Time | Focus | Milestone |
|---|---|---|
| Week 1 | Requirements, source review, and AWS fundamentals | QuickBite scope defined |
| Week 2 | Business-flow completion and PostgreSQL | Stable local baseline |
| Week 3 | Containerization, testing, and security review | Consistent backend/frontend runtime |
| Week 4 | Architecture and cost assessment | Two-AZ HA architecture finalized |
| Week 5 | Terraform bootstrap and network module | Remote state, ECR, and VPC available |
| Week 6 | Data and application modules | RDS, ALB, ASG, and EC2 available |
| Week 7 | Frontend deployment, CloudFront, and functional testing | Website and API available through HTTPS |
| Week 8 | Monitoring, alarms, cost control, and evidence | System and report completed |

## 10. Budget estimate and cost control

### 10.1. Budget objective

The deployment is controlled within the AWS credits available to the program, with a target of remaining below **USD 200 in credits** for the complete workshop period.

Actual cost depends on runtime, traffic, request volume, and log ingestion. I therefore use AWS Budgets and Cost Explorer to track real spending rather than asserting a fixed value before the environment has run.

### 10.2. Main cost groups

| Resource group | Cost driver | Control decision |
|---|---|---|
| EC2 Auto Scaling | Instance count and runtime | Use t3.micro, desired capacity 2, and destroy after the demo |
| Multi-AZ RDS | Database instance, storage, and standby | Use db.t3.micro, limited storage, and limited runtime |
| NAT Gateway | Gateway-hours and processed data | Monitor in Cost Explorer and keep only while needed |
| Application Load Balancer | Runtime and LCUs | Use a single ALB for the API |
| CloudFront | Requests and data transfer | Cache static assets and menu images |
| S3 | Storage and requests | Separate buckets and remove temporary objects |
| ECR | Image storage | Remove old tags and retain only required versions |
| CloudWatch | Log ingestion and retention | Set retention and avoid extended debug logging |
| Secrets Manager | Number of secrets | Use one controlled application secret |
| SNS | Notification volume | Send only required alarms |

### 10.3. Cost controls

- Configure AWS Budget and email alerts.
- Apply common tags to all resources.
- Review Cost Explorer by service.
- Use small instances suitable for demo traffic.
- Limit log retention.
- Exclude custom domain, WAF, and nonessential services from the current scope.
- Run Terraform destroy after the demo is complete.

## 11. Risk assessment

| Risk | Impact | Likelihood | Mitigation |
|---|---|---|---|
| Cost overrun | High | Medium | Budgets, Cost Explorer, tagging, right-sizing, and destroy |
| Credential exposure | High | Low | IAM roles, Secrets Manager, and no root keys in the application |
| EC2 or AZ failure | Medium | Low | ALB, ASG min 2, and two-AZ deployment |
| Database failure | High | Low | Multi-AZ RDS, synchronous standby, and isolated Security Group |
| CloudFront route collision | Medium | Medium | Separate web/image and API distributions |
| Image upload failure | Medium | Medium | Scoped IAM permissions, API validation, and CloudFront behavior |
| EC2 cannot pull the image | High | Medium | ECR permissions, NAT Gateway, and health checks |
| Missing operational logs | Medium | Medium | CloudWatch Logs, metrics, and alarms |
| Concurrent Terraform updates | High | Low | S3 remote state and DynamoDB state locking |
| Required data removed during cleanup | High | Low | Confirm retained data before Terraform destroy |

### Contingency plan

- If an EC2 instance fails, ALB removes the unhealthy target and ASG creates a replacement.
- If an Availability Zone fails, application capacity remains in the other AZ.
- If the RDS primary fails, Multi-AZ supports failover to the standby.
- If a new version is unstable, the previous Docker image version can be deployed from ECR.
- If cost rises unexpectedly, the demo environment can be stopped or destroyed after required evidence is saved.

## 12. AWS Well-Architected alignment

| Pillar | Implementation |
|---|---|
| Operational Excellence | Terraform, remote state, state locking, modules, health checks, CloudWatch, and deployment documentation |
| Security | Private S3 + OAC, private EC2, isolated RDS, Security Group chaining, IAM role, Secrets Manager, and SSM |
| Reliability | Two Availability Zones, ALB, ASG min 2, health checks, Multi-AZ RDS, and automatic replacement |
| Performance Efficiency | CloudFront caching, ALB distribution, ASG target tracking, and right-sized t3.micro instances |
| Cost Optimization | AWS Budgets, Cost Explorer, right-sizing, log retention, and Terraform destroy |
| Sustainability | Managed services, Auto Scaling, retention controls, and cleanup after the demo |

## 13. Expected outcomes

### 13.1. Technical outcomes

- QuickBite is publicly available over HTTPS.
- Frontend, backend, database, and object storage have separated responsibilities.
- The backend runs on two EC2 instances across two Availability Zones behind an ALB.
- The database uses Multi-AZ RDS PostgreSQL.
- Menu images are stored in private S3 and delivered through CloudFront.
- Secrets are managed through Secrets Manager.
- Logs, metrics, alarms, and email notifications operate correctly.
- Infrastructure can be recreated through Terraform.

### 13.2. Operational value

- Less dependence on manual console configuration.
- Traceable infrastructure changes.
- Health checks and operational alerts.
- Cost visibility by AWS service.
- A foundation for future CI/CD, WAF, custom domain, and backup improvements.

### 13.3. Deliverables

- QuickBite source code.
- QuickBite website deployed on AWS.
- High-availability AWS architecture.
- Terraform code for bootstrap and main stacks.
- AWS Console and demo evidence.
- Bilingual FCAJ report.

## 14. Conclusion

The expected result is to transform QuickBite from a local application into a cloud system with clear architecture, availability, security, observability, and repeatable deployment. Terraform makes infrastructure part of the product rather than a collection of isolated manual AWS Console operations.