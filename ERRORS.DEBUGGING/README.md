Debugging & Troubleshooting

During development, several configuration and permission issues were encountered while wiring the services together. Below are the key errors and how they were resolved.

1️⃣ API Route Error – “Not Found”

When testing the API Gateway invoke URL, the response returned { "message": "Not Found" }. This occurred because the base stage URL was accessed without specifying the correct route path. The API was configured with a POST /orders route, but the request was made to /prod instead of /prod/orders. Additionally, using a browser sent a GET request while the route expected POST. The issue was resolved by calling the full endpoint path /prod/orders with the correct HTTP method, which successfully routed the request to the Create Order Lambda.

2️⃣ SQS Trigger Permission Error

While attaching the SQS queue as a trigger to the Process Order Lambda, AWS returned an error indicating that the execution role did not have permission to call ReceiveMessage on SQS. The root cause was that the Lambda execution role lacked the required SQS permissions. This was resolved by attaching the managed policy AWSLambdaSQSQueueExecutionRole to the Process Order Lambda role, granting the necessary permissions for polling and deleting messages from the queue. After this update, the trigger was successfully configured.

3️⃣ Lambda Handler Error – “event is not defined”

During testing of the Process Order Lambda, execution failed with a ReferenceError: event is not defined. The issue was caused by referencing the event object without defining it in the handler function signature. The handler was corrected to properly accept the event parameter using export const handler = async (event) => { ... }, ensuring the function could process the incoming SQS event. Once corrected, the Lambda executed successfully.

4️⃣ CloudWatch Log Group Does Not Exist

While checking CloudWatch logs for the Process Order Lambda, the log group was not found. This happened because the Lambda had not yet executed successfully, and CloudWatch automatically creates the log group only after the first invocation. After resolving the permission and handler errors and triggering the function via SQS, the log group was automatically created and logs became available.

5️⃣ IAM SendMessage Permission Misconfiguration

The Create Order Lambda initially failed to send messages to SQS due to insufficient IAM permissions. The execution role did not allow the sqs:SendMessage action on the main orders queue ARN. In one instance, the policy mistakenly referenced the Dead Letter Queue instead of the primary queue. The issue was resolved by updating the IAM policy to allow sqs:SendMessage on the correct SQS queue ARN. After applying the corrected policy, messages were successfully delivered to the queue.
