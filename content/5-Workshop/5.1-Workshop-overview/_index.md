---
title: "Overview"
date: 2026-07-31
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---
# Workshop Overview

## 1. QuickBite problem

QuickBite digitizes ordering and fulfillment among customers, administrators, kitchen staff, and delivery staff. The FastAPI backend handles authentication, menus, orders, payments, reports, audit logs, and image uploads. PostgreSQL stores transaction data, while React provides the storefront and role-specific interfaces.

## 2. Business flow

1. A customer browses the menu and creates an order.
2. The backend validates input, calculates totals on the server, and writes the order to PostgreSQL.
3. An administrator confirms or manages the order.
4. Kitchen staff move the order through preparation states.
5. Delivery staff receive ready orders and complete delivery.
6. The customer tracks status through an account or lookup code.

## 3. Deployed AWS architecture

<img src="/images/5-Workshop/5.1-Workshop-overview/internship.png" alt="QuickBite AWS architecture diagram" width="100%" />
<p><em>The two-Availability-Zone high-availability architecture deployed with Terraform.</em></p>

The architecture has three main layers:

- **Global edge:** CloudFront provides HTTPS and caching for the frontend, menu images, and API;
- **Application layer:** the ALB distributes requests to an EC2 Auto Scaling Group in two private subnets;
- **Data layer:** Multi-AZ RDS PostgreSQL resides in isolated database subnets.

ECR, Secrets Manager, Systems Manager, CloudWatch, SNS, IAM, Budgets, and Cost Explorer support deployment, security, observability, and operations.

## 4. Two CloudFront distributions

I separated CloudFront into two distributions:

- **Web distribution:** the default origin is the frontend S3 bucket, with **/menu/** routed to the menu-image S3 bucket;
- **API distribution:** the origin is the Application Load Balancer, with caching disabled for API requests.

This avoids the collision between the React **/menu** route and the FastAPI **/menu** endpoint, while keeping user-facing HTTPS without purchasing a custom domain.

## 5. High availability

- the ALB spans two public subnets in two Availability Zones;
- the Auto Scaling Group maintains two EC2 instances in two private subnets;
- the Launch Template keeps AMI, IAM profile, user data, and Docker configuration consistent;
- Multi-AZ RDS provides synchronous standby and managed failover;
- the ALB uses **/health** to remove unhealthy targets;
- the target-tracking scaling policy monitors average Auto Scaling Group CPU.

## 6. Deployment result

The frontend and API operate through CloudFront. Both EC2 instances are Running with 3/3 status checks passed. The API health response reports service **quickbite-api**, version **1.4.0**. The admin dashboard, customer menu, image upload, CloudWatch alarms, and SNS email are evidenced in the Validation section.