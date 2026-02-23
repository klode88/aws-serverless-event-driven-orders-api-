# 🔎 Debugging & Error Resolution (Step-by-Step)

This section documents real issues encountered during implementation and how they were resolved.

---

## 1️⃣ API Gateway – "Not Found" Error

When testing the API endpoint using the stage URL, the response returned:

{
  "message": "Not Found"
}

This occurred because the request was sent to the base stage URL (`/prod`) without including the defined resource path (`/orders`). API Gateway requires the correct route path to match the configured integration.

**Resolution:**  
Updated the request URL to include `/orders`, ensuring it matched the POST route configured in API Gateway.

---

## 2️⃣ SQS Trigger Permission Error

While configuring the SQS trigger for the `process-order` Lambda, AWS returned:

"InvalidParameterValueException: The function execution role does not have permissions to call ReceiveMessage on SQS"

This occurred because the Lambda execution role lacked required SQS permissions.

**Resolution:**  
Attached a policy to the process-order Lambda role granting:

- sqs:ReceiveMessage
- sqs:DeleteMessage
- sqs:GetQueueAttributes

After attaching the correct policy, the trigger was successfully configured.

---

## 3️⃣ Create Lambda Missing SQS Send Permission

The create-order Lambda initially did not have permission to send messages to the SQS queue.

**Resolution:**  
Attached an IAM policy allowing:

- sqs:SendMessage

This enabled the create-order Lambda to push orders to the queue successfully.

---

## 4️⃣ Lambda Runtime Error – "event is not defined"

While testing the process-order Lambda manually, execution failed with:

ReferenceError: event is not defined

This occurred because the handler function did not properly define or receive the `event` parameter.

**Resolution:**  
Updated the Lambda handler to correctly accept and reference the event object.

After fixing the handler signature, the function executed successfully.

---

## 5️⃣ CloudWatch Log Group Does Not Exist

When attempting to view logs for the process-order Lambda, CloudWatch returned:

"Log group does not exist"

This happened because the Lambda had never executed successfully, and CloudWatch log groups are only created upon first execution.

**Resolution:**  
Triggered the Lambda successfully via SQS, which automatically created the log group.

Logs were then available for inspection.

