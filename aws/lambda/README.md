## AWS Lambda

Serverless Cloud Computing, Function as a Service (FaaS).
Simply put, you are not managing any servers.

![serverless.png](serverless.png)

Run code snippets in serverless way,
without server management.
**Lambda is a stateless service**.
**You are paying only for the time that you use**:
you can provision up to 10GB RAM per function.

Supported platforms:
1. NodeJS
2. Python
3. Java
4. Golang
5. Ruby
6. C#, Powershell
7. Custom runtime API (community supported)

Lambda Container Image must implement the Lambda Runtime API,
**ECS/Fargate is preferred for running arbitrary Docker images**.

Use-cases:
1. Real-time stream processing
2. Real-time file processing
3. ETL
4. Integration inside AWS
5. Cron jobs

**Lambda is a time-bound service: the maximum timeout is 15 minutes (900 seconds)**. 

### Serverless Integration

* API Gateway
* DynamoDB
* Cognito
* S3
* Kinesis
* EventBridge
* CloudFront

Lambda anti-patterns:
1. Long-running applications: use EC2 instead, or chain functions
2. Dynamic websites with AJAX
3. Stateful applications

**Each Lambda function has an IAM roles**.

### Sync Invocation

CLI, SDK, API Gateway, Application Load Balancer.
You are waiting for a result,
errors should be handled on the client side (retries).

![sync.png](sync.png)

Services that are synchronous:
* User invoked:
  * ELB
  * API Gateway
  * S3 Batch
  * CloudFront
* Service invoked:
  * Cognito
  * Step Functions
  * Lex
  * Alexa
  * Kinesis Data Firehose

### Lambda + ELB

A Lambda function must be registered in a **target group**.

ALB transform HTTP request to JSON, as well as JSON to HTTP for response:

![elb.png](elb.png)

All the responses should have the same structure:

```json
{
  "statusCode": 200,
  "statusDescription": "200 OK",
  "isBase64Encoded": False,
  "headers": {
    "Content-Type": "text/html"
  },
  "body": "<h1>Lambda Example</h1>"
}
```

#### Multi-Header Values

ALB can support multi-header values (need to enable it in ALB).
When you enable it, values for them will be translated to
arrays inside the JSON request:

![mv.png](mv.png)
