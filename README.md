## Scalable Web Application with ALB and Auto Scaling

---

## Overview

This project demonstrates how to deploy a **highly available and scalable web application** using AWS EC2 instances with an **Application Load Balancer (ALB)** and **Auto Scaling Groups (ASG)**. It adheres to best practices for **compute scalability**, **fault tolerance**, and **cost optimization**.

---

## Architecture Type

**EC2-based Scalable Web Application**

---

## AWS Services Used

| Service | Purpose |
|--------|---------|
| **EC2** | Hosts the web and application servers |
| **ALB (Application Load Balancer)** | Distributes traffic across EC2 instances |
| **ASG (Auto Scaling Group)** | Dynamically adjusts number of EC2 instances |
| **Amazon RDS (Optional)** | Stores application data using MySQL/PostgreSQL |
| **IAM** | Grants controlled access to services |
| **CloudWatch & SNS** | Monitors infrastructure and sends notifications |

---

## IAM Roles & Policies

| Component | IAM Role/Policy |
|----------|------------------|
| **ALB** | `AWSElasticLoadBalancingServiceRolePolicy` |
| **ASG** | `AWSServiceRoleForAutoScaling` |
| **CloudWatch** | `CloudWatchReadOnlyAccess` |

---

## Security Group Configuration

| Component | Inbound Traffic | Outbound Traffic |
|-----------|------------------|------------------|
| **Web ALB** | `0.0.0.0/0` on ports `80, 443` | Web EC2 Subnets (ports `80, 443`) |
| **Web EC2** | Web ALB IPs (ports `80, 443`) | App ALB IPs (port `5000`) |
| **App ALB** | Web EC2 Subnet Range (port `5000`) | App EC2 Subnet Range (port `5000`) |
| **App EC2** | App ALB IPs (port `5000`) | RDS Subnets (port `3306`) |
| **RDS** | App EC2 Subnets (port `3306`) | - |

---

## NAT Gateway & Bastion Host

- **NAT Gateways** are used for secure updates or patches on private instances.
- A **Jump-Host EC2** (Bastion) is provided for administrative SSH access to private instances.

---

## Project Flow

1. **User Request:**
   - The external user accesses the web application via a **Route 53** domain.
   - The request routes through the **Internet Gateway** to the **Public ALB (Web Tier)**.

2. **Web Tier:**
   - The **Web ALB** balances the request load across **Web EC2 instances** within an **Auto Scaling Group**.
   - Based on demand:
     - **ASG scales up** during high load.
     - **ASG scales down** when the load reduces.

3. **Application Tier:**
   - Web Tier forwards requests to the **private ALB (App Tier)**.
   - App Tier ALB balances the traffic across private **App EC2 instances** using its own **ASG**.

4. **Database Tier:**
   - App EC2 queries the **Amazon RDS** for backend data.
   - **Multi-AZ RDS** ensures failover support and high availability.

5. **Monitoring & Alerts:**
   - **Amazon CloudWatch** tracks:
     - EC2 instance lifecycle events (launch, terminate).
     - RDS failovers and health.
   - When an event occurs:
     - **SNS** sends email alerts to the **Admin** subscribed to the topic.

---

## Scalability & Fault Tolerance

- **Horizontal Scaling**: ASG automatically scales EC2 instances based on traffic metrics.
- **Multi-AZ Deployment**: Ensures both web/app tiers and RDS remain available during AZ failure.
- **CloudWatch + SNS**: Guarantees visibility and quick response to infrastructure events.

---

## Optional Enhancements

- 🔐 Add WAF (Web Application Firewall) in front of ALB.
- 🌍 Use CloudFront CDN for global caching and speed.
- 🧪 Add CI/CD pipelines (e.g., CodePipeline) for automated deployment.
- 🔐 Integrate Secrets Manager or Parameter Store for credentials.

---

