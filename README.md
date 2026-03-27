Event-Driven Orders API (Cost-Aware Serverless Architecture)
This project implements a serverless, event-driven order processing system on AWS.
It accepts customer orders through an API, queues them for asynchronous processing, and publishes notifications after successful processing.
The architecture is designed to be:
Scalable
Fault-tolerant
Loosely coupled
Production-aware

📌 Overview

This project demonstrates how a modern backend can process orders without relying on always-running servers.
Instead of keeping infrastructure running 24/7, the system uses on-demand AWS services so it can:
respond quickly to incoming requests
scale automatically when traffic increases
remain efficient when traffic is low
isolate failures without breaking the whole system
This makes it a strong example of real-world cloud design focused on simplicity, resilience, and scalability.

🏗 Architecture

Services Used
Amazon API Gateway (HTTP API)
AWS Lambda
Amazon SQS (Standard Queue)
Amazon SNS
Amazon SQS Dead Letter Queue (DLQ)
AWS IAM
Amazon CloudWatch

🔄 System Flow

1. Customer submits an order
A client sends a POST /orders request to the API.
2. API Gateway receives the request
Amazon API Gateway acts as the public entry point for the application and forwards the request to Lambda.
3. Lambda creates the order
The first Lambda function:
validates the request
generates an orderId
sends the order message to SQS
immediately returns a response such as Order accepted
This keeps the API fast and responsive.
4. SQS stores the order message
Amazon SQS safely stores the order in a queue.
This allows the system to:
handle bursts of traffic
decouple request handling from backend processing
avoid losing orders during temporary issues
5. Lambda processes the order asynchronously
A second Lambda function is triggered by SQS.
It performs the backend processing logic, such as:
handling the order workflow
simulating payment or inventory actions
preparing downstream notification events
6. SNS publishes a notification
After successful processing, Amazon SNS publishes an event.
This can notify:
email subscribers
other applications
future microservices
7. Failed messages move to the DLQ
If processing repeatedly fails, the message is moved to a Dead Letter Queue.
This improves reliability by making failed events visible for investigation instead of silently losing them.

⚙️ Why These Services Were Chosen

Amazon API Gateway (HTTP API)

Why used:

Lowest-cost API Gateway option
Good fit for simple API endpoints such as POST /orders
Fully managed, so no servers to maintain
Automatically scales with incoming traffic

Alternatives:

REST API Gateway
A stronger choice when advanced API features are needed, such as API keys, usage plans, request validation, or more detailed controls.

Application Load Balancer (ALB)
Better suited for container or server-based architectures (ECS/EC2) rather than lightweight serverless APIs.

AWS Lambda

Why used:

Fully serverless with no infrastructure to manage
Automatically scales based on demand
Pay only when code runs
Ideal for short, event-driven tasks
Keeps the architecture simple and lightweight
Used in this project for:
creating the order
processing the queued order asynchronously

Alternatives:

AWS Step Functions
Suitable for multi-step workflows involving branching logic, retries, or state tracking. Not required here because the flow is simple and handled efficiently with Lambda and SQS.

Amazon ECS (Fargate)

Better for long-running or containerised workloads where more runtime control is needed.

Amazon EC2

Provides full control but requires server management and introduces continuous running costs.

Amazon SQS (Standard Queue)

Why used:

Extremely low-cost messaging service
Decouples the API layer from backend processing
Buffers sudden traffic spikes
Helps prevent message loss
Supports reliable asynchronous design

Alternatives:

SQS FIFO Queue

Used when strict message ordering and deduplication are required.

Amazon EventBridge

Better for advanced event routing and integration across multiple services.

Amazon SNS

Why used:

Enables fan-out architecture
Allows one event to notify multiple subscribers
Keeps services loosely coupled
Easy to extend with additional integrations

Alternatives:

Amazon EventBridge

Better suited for complex event-driven systems requiring filtering and routing rules.
Direct Lambda invocation
Simpler but introduces tighter coupling between services.

Dead Letter Queue (DLQ)

Why used:

Captures messages that fail processing
Prevents data loss
Supports debugging and monitoring
Improves system reliability

Alternatives:

Retry-only approach
Simpler setup but lacks visibility into failed messages.

Step Functions
error handling
Provides structured retry logic, but adds complexity not needed for this design.

AWS IAM

Why used:

Enforces least-privilege access
Ensures services only perform required actions
Improves security posture
Aligns with AWS best practices
Typical permissions include:
sqs:SendMessage
sns:Publish
CloudWatch logging permissions

Alternatives:

Broad permissions
Easier to configure but insecure and not suitable for production.

Shared roles

Faster to set up but weaker from a security and auditing perspective.

🧠 Architectural Benefits

This project demonstrates key cloud architecture principles:
Asynchronous processing
Loose coupling between services
Automatic scaling
Failure isolation
Observability through logs and DLQ
Efficient use of managed services

🏢 Real Business Use Case

Example: E-commerce platform
Customer places an order
API instantly accepts the request
Order is safely queued
Backend processes it asynchronously
Notifications are published
Failures are captured in the DLQ
This design is effective during high-traffic events such as:
Black Friday
flash sales
seasonal demand spikes
It prevents system overload while maintaining a responsive user experience

📎 Project Summary

This project demonstrates a serverless, event-driven architecture using:
API Gateway
Lambda
SQS
SNS
Dead Letter Queue
IAM
CloudWatch
It showcases understanding of:
cloud service selection
event-driven design patterns
scalable system architecture
fault tolerance
secure access control
production-level thinking
