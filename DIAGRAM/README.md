Architecture 🔄 System Flow (Step-by-Step)

1️⃣ Customer submits an order A client sends a POST /orders request to the API.

2️⃣ API Gateway receives the request (HTTP API)

Chosen because it is the cheapest API Gateway option

Lightweight and cost-efficient

Forwards the request to Lambda

3️⃣ Lambda (Create Order) executes,Validates the input

Generates an orderId

Sends the order to SQS

Immediately returns “Order accepted” to the customer

4️⃣ SQS stores the message,Buffers traffic spikes

Decouples frontend from backend

Ensures no order is lost

5️⃣ Lambda (Process Order) is triggered automatically

Processes the order asynchronously

Executes business logic (e.g., payment, inventory, notification)

6️⃣ SNS publishes an event,Sends notification to subscribers

Can notify email, other services, or future microservices

7️⃣ If processing fails,Message is retried automatically
