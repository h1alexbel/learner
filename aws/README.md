# AWS

Each resource has it unique **ARN** (Amazon Resource Name):
`arn:aws:s3::..`

## AWS Regions

Factors for choosing a particular AWS Region:
1. Compliance with data governance and legal requirements: data never leaves a region without your explicit permission.
2. Proximity to customers: reduced **latency**.
3. Available services withing an AWS Region: new services and new features aren't available in every Region.
4. Pricing: pricing varies region to region.

Each region has unique code.
Each region has at least **3 availability zones** (AZs).
**Min is 3, max is 6 AZs.**

AWS has 400+ POPs (or Edge Locations), 10+ Regional Caches in 90+ cities across 40+ countries.
Content is delivered to end users with lower latency.

AWS has Global Services:
1. AWS IAM
2. Route 53
3. CloudFront
4. WAF

Most AWS services are **Region-scoped**.

Table of contents:
1. [IAM](iam/README.md)
2. [EC2](ec2/README.md)
3. [ELB](elb/README.md)
4. [RDS, Aurora](rds/README.md)
5. [Caching](caching/README.md)
6. [Route 53, DNS on AWS](dns/README.md)
7. [VPC](vpc/README.md)
8. [S3](s3/README.md)
9. [CloudFront, CDN on AWS](cloudfront/README.md)
10. [ECS, Fargate, and EKS](ecs/README.md)
11. [IaC with CloudFormation and Beanstalk](cloudformation/README.md)
12. [Messaging](messaging/README.md)
13. [Monitoring](monitoring/README.md)
14. [Lambda](lambda/README.md)
15. [DynamoDB](dynamo/README.md)
16. API Gateway
17. CI/CD on AWS
18. SAM
19. CDK
20. Cognito
21. KMS
