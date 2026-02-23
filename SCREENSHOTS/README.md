
1️⃣ HTTP API Created (POST /orders)

Created HTTP API with POST /orders route.
Chosen because it is the cheapest API Gateway option and fully serverless.

02️⃣ Integration Attached to Lambda

Connected the HTTP API route to the Create Order Lambda.

03️⃣ Stage (prod) Created

Deployed API to prod stage so it becomes publicly accessible.

04️⃣ Invoke URL

API tested using the invoke URL to confirm routing works.

05️⃣ First Lambda Created (Create Order)

This Lambda:

Validates request

Generates orderId

Sends message to SQS

Returns “Order accepted”

06️⃣ Environment Variable (QUEUE_URL)

Configured QUEUE_URL so the Lambda can send messages to SQS.

07️⃣ IAM Role Attached

Attached IAM role with:

sqs:SendMessage

CloudWatch logging permissions

08️⃣ Function Tested Successfully

Confirmed Lambda execution works correctly.

09️⃣ SQS Dead Letter Queue Created

Created DLQ to handle failed messages after retries.

🔟 Second Lambda Created (Process Order)

Created asynchronous processing Lambda triggered by SQS.

11️⃣ SQS Trigger Enabled

Connected SQS queue to second Lambda as an event source.

12️⃣ Successful Invocation (Processing)

Verified message was processed successfully.

13️⃣ SNS Topic Created

Created SNS topic for notifications after order processing.

14️⃣ Email Subscription Created

Subscribed email endpoint to SNS topic for order notifications.
