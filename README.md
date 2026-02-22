Project 4 – Event-Driven Orders API (Cost-Aware Serverless Architecture)
 Overview

This project implements a serverless, event-driven order processing system on AWS.

It accepts customer orders through an API, queues them for asynchronous processing, and publishes notifications after successful processing.

The architecture is intentionally designed to be:

Scalable

Fault-tolerant

Cost-efficient

Loosely coupled

Production-ready

🏗 Architecture
🔄 System Flow

Client
→ API Gateway (HTTP API)
→ Lambda (Create Order)
→ SQS (Orders Queue)
→ Lambda (Process Order)
→ SNS (Notifications)
→ Email / Subscribers

If processing fails → Dead Letter Queue (DLQ)

📊 Simple Architecture Diagram
        Client
           │
     POST /orders
           │
     API Gateway (HTTP API)
           │
   Lambda – Create Order
           │
       SQS Queue
           │
   Lambda – Process Order
           │
         SNS Topic
           │
     Email / Subscribers
           │
          DLQ (if failure)
🧱 Services Used & Trade-Offs
1️⃣ API Gateway – HTTP API
Why used

Cheapest API Gateway option

Low latency

Simple integration with Lambda

Pay-per-request pricing

Trade-off

Fewer advanced features than REST API

No built-in console “Test” button

When to use REST API instead

API keys & usage plans required

Advanced request validation

Full API lifecycle management

2️⃣ Lambda – Create Order
Why used

Serverless (no idle cost)

Auto-scaling

Fast synchronous response

Short execution time = low cost

Trade-off

Execution time limits

Cold starts (minimal impact here)

Alternative

ECS / EC2 for long-running or compute-heavy workloads

3️⃣ SQS – Standard Queue
Why used

Extremely low cost

Buffers traffic spikes

Decouples API from processing

Built-in retry mechanism

Trade-off

At-least-once delivery

Messages may arrive out of order

When to use FIFO instead

Strict ordering required

Deduplication needed

4️⃣ Lambda – Process Order
Why used

Asynchronous background processing

Scales automatically with queue depth

Clean separation of responsibilities

Trade-off

Requires monitoring & DLQ handling

5️⃣ SNS – Notifications
Why used

Fan-out architecture (one event → multiple subscribers)

Loose coupling between services

Easy email integration

Trade-off

Additional cost per delivery

Must manage subscriptions

Alternative

EventBridge for complex routing

Step Functions for multi-step workflows

6️⃣ Dead Letter Queue (DLQ)
Why used

Prevents data loss

Captures failed messages

Enables operational debugging

Trade-off

Requires monitoring and manual review

7️⃣ IAM – Least Privilege Security

Execution roles allow only required actions:

sqs:SendMessage

sns:Publish

CloudWatch logging

This follows security best practices.

💰 Cost Optimization Strategy

This system avoids:

EC2 instances running 24/7

Overprovisioned infrastructure

Paying for idle compute

Cost Model
Service	Pricing Model
HTTP API	Per request
Lambda	Per execution time
SQS	Per request
SNS	Per publish/delivery
CloudWatch	Log ingestion/storage
Why This Is Cost-Efficient

No traffic → almost zero compute cost

Short Lambda duration keeps billing low

SQS absorbs spikes without scaling servers

No infrastructure maintenance

Designed intentionally for non-Free-Tier usage

This is true pay-as-you-go architecture.

🧠 Architectural Benefits

Asynchronous processing

Loose coupling

Automatic horizontal scaling

Built-in retries

Failure isolation via DLQ

Full observability via CloudWatch

🏢 Real Business Use Case

For an e-commerce platform:

Customer places order.

API immediately accepts it.

Order is queued safely.

Backend processes payment/inventory.

SNS notifies other systems.

Failures move to DLQ for review.

This prevents overload during high-traffic events (e.g., Black Friday) while keeping infrastructure costs low.

🔮 Possible Enhancements

Add DynamoDB for persistent storage

Add Cognito for authentication

Add WAF for protection

Add CloudWatch alarms

Add idempotency protection

Implement Infrastructure as Code (Terraform)

Add CI/CD pipeline

📎 Summary

This project demonstrates a cost-aware, event-driven, serverless architecture using:

API Gateway + Lambda + SQS + SNS + DLQ

It showcases understanding of:

Cloud trade-offs

Cost optimization

Scalability patterns

Fault tolerance

Secure IAM configuration

Real production design thinking
