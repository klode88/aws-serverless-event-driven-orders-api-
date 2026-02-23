🛠 Errors & Debugging – Event Driven Orders API

 1 --We first encountered an API Not Found error when testing the deployed API Gateway endpoint because we called only the stage URL instead of the full route path. The API was correctly deployed, but the request did not include the configured resource path. After calling the correct POST /orders route under the stage, the Create Lambda triggered successfully and the issue was resolved.

2--When configuring SQS to trigger the Process Lambda, we received a permissions error stating that the function execution role did not have permission to call ReceiveMessage on SQS. This happened because the Process Lambda IAM role did not include SQS consumer permissions. We fixed this by attaching a policy allowing ReceiveMessage, DeleteMessage and GetQueueAttributes to the specific queue, after which the trigger was created successfully.

3--After attaching the correct IAM policy to the Process Lambda role, the SQS trigger worked and the Lambda was able to receive and process messages. This confirmed that the event source mapping between SQS and the Process Lambda was correctly configured and that permissions were no longer blocking execution.

4--The Create Lambda initially failed to send messages to SQS because its execution role did not include the SendMessage permission. We resolved this by adding an IAM policy allowing sqs SendMessage to the specific queue ARN. Once attached, the Create Lambda successfully pushed orders to SQS and returned Order Accepted to the client.

5--During manual testing of the Process Lambda, execution failed with a runtime error saying event is not defined. This was caused by an incorrect handler definition that did not include the event parameter. We corrected the handler function signature to properly accept the event object, and the Lambda executed successfully afterward.

6--When attempting to view logs in CloudWatch, we saw a log group does not exist error. This occurred because the Lambda had not yet executed successfully, and CloudWatch only creates the log group after the first invocation. After triggering a successful execution, the log group was automatically created and logs became available.
