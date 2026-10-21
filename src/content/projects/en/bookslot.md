---
title: "Bookslot"
description: "API for managing bookings in limited-capacity spaces such as gyms or coworking rooms."
pubDate: 2026-07-02
cover: "https://images.unsplash.com/photo-1549576490-b0b4831ef60a?q=80&w=1170&auto=format"
tags: ["AWS", "DynamoDB", "Terraform"]
featured: true
links:
  - label: "GitHub"
    url: "https://github.com/thaishdz/bookslot"
    icon: "simple-icons:github"
toc: "center"
---

## Overview

BookSlot is an API for booking spaces with limited capacity, such as gyms or coworking rooms.

![Booking flow](https://raw.githubusercontent.com/thaishdz/bookslot/main/docs/assets/flujo-reserva.png)

Instead of running a server that's up 24/7 waiting for sporadic traffic, I designed the whole system as a serverless architecture on AWS (pay per use, zero cost at rest, and it scales on its own if traffic grows). The result is a booking API built around slots (openings with a date, time and capacity) where every booking is processed safely even under real concurrency:

- **Compute**: AWS Lambda (on-demand functions)
- **API**: API Gateway (HTTP API)
- **Database**: DynamoDB (NoSQL, single-table)
- **Authentication**: Amazon Cognito (JWT)
- **IaC**: Terraform
- **CI/CD**: GitHub Actions
- **Observability**: CloudWatch (logs + alarms) + SNS

> [!NOTE]
> Bookslot is my final project for the AWS & Cloud course certificate at [Commit Academy](https://www.commitacademy.io/).

## Goals

- **Prevent overbooking under real concurrency**, so a booking is never confirmed for a space that's already full.
- **Near-zero operating cost**, enough for an MVP with almost no traffic.
- **Reproducible infrastructure**, managed entirely with Terraform instead of manual clicks in the AWS console.
- **Observability**, so if something breaks in production, I find out from the alarms before a user complains.
- **Active cost control**, with AWS Budgets alerting me before spending goes beyond what's expected.

## Architecture decisions (ADRs)

- [ADR-0001: Native state locking in S3](https://github.com/thaishdz/bookslot/blob/main/docs/adr/0001-state-locking-s3-native.md)
- [ADR-0002: Single-table data model](https://github.com/thaishdz/bookslot/blob/main/docs/adr/0002-single-table-design.md)
- [ADR-0003: Lambda IAM execution role](https://github.com/thaishdz/bookslot/blob/main/docs/adr/0003-lambda-iam-execution-role.md)
- [ADR-0004: Concurrency control](https://github.com/thaishdz/bookslot/blob/main/docs/adr/0004-concurrency-control.md)
- [ADR-0005: CI/CD pipeline authentication with AWS](https://github.com/thaishdz/bookslot/blob/main/docs/adr/0005-cicd-authentication.md)
- [ADR-0006: Manual bootstrap of the Terraform backend](https://github.com/thaishdz/bookslot/blob/main/docs/adr/0006-terraform-backend-bootstrap.md)
- [ADR-0007: Authentication and authorization with AWS Cognito](https://github.com/thaishdz/bookslot/blob/main/docs/adr/0007-cognito-authentication-authorization.md)
- [ADR-0008: Booking observability](https://github.com/thaishdz/bookslot/blob/main/docs/adr/0008-observability-booking-alarm.md)

## FinOps

### Tags
Every resource carries tags (`Project`, `Environment`) applied through `default_tags` in the provider, which makes it possible to filter spending by project in **Cost Explorer**.

### Cost estimate
With MVP-level traffic, BookSlot runs **within the AWS free tier**:

| Service        | Estimated usage (MVP) | Free tier                | Cost |
|----------------|-----------------------|--------------------------|------|
| Lambda         | ~100 req/month        | 1M req + 400k GB-s/month | €0   |
| DynamoDB       | ~200 ops/month        | 200M req + 25 GB/month   | €0   |
| API Gateway    | ~100 req/month        | 1M req/month             | €0   |
| CloudWatch/SNS | minimal volume        | within free tier         | ~€0  |
| S3 (tfstate)   | a few KB              | —                        | ~€0  |

**Total estimated cost: ~€0/month**. The whole system runs within the free
tier with the real traffic of an academic MVP (~100 req/month).

The **AWS Budgets** forecast confirms **$0.12/month projected and $0 spent**.

>NOTE: Prices verified in July 2026

### Spending control
An AWS Budget (`bookslot-dev-monthly`, $10/month limit) with tiered thresholds sends email alerts on deviations:
- **50%** ($5) → early warning (actual spend)
- **80%** ($8) → alert (actual spend)
- **100%** ($10) → predictive warning (forecast)
