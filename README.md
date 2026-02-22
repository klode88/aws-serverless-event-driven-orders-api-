Project 4 – Event-Driven Orders API (Cost-Aware Serverless Architecture)
 Overview

This project implements a serverless, event-driven order processing system on AWS.

It accepts customer orders through an API, queues them for asynchronous processing, and publishes notifications after successful processing.

The architecture is intentionally designed to be:

Scalable-Fault-tolerant-Cost-efficient-Loosely coupled-Production-ready

🏗 Architecture
🔄 System Flow

2️⃣ Lambda – Create Order
Why used

Serverless (no idle cost)

Auto-scaling

Fast synchronous response

Short execution time = low cost

-Trade-off

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

-Trade-off

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

-Trade-off

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

-Trade-off

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

Architecture
🔄 System Flow (Step-by-Step)

1️⃣ Customer submits an order
A client sends a POST /orders request to the API.

2️⃣ API Gateway receives the request (HTTP API)

Chosen because it is the cheapest API Gateway option

Lightweight and cost-efficient

Forwards the request to Lambda

3️⃣ Lambda (Create Order) executes

Validates the input

Generates an orderId

Sends the order to SQS

Immediately returns “Order accepted” to the customer

4️⃣ SQS stores the message

Buffers traffic spikes

Decouples frontend from backend

Ensures no order is lost

5️⃣ Lambda (Process Order) is triggered automatically

Processes the order asynchronously

Executes business logic (e.g., payment, inventory, notification)

6️⃣ SNS publishes an event

Sends notification to subscribers

Can notify email, other services, or future microservices

7️⃣ If processing fails

Message is retried automatically 

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
