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

But most AWS services are **Region-scoped**.

## AWS IAM
IAM — Identity and Access Management.

### Accounts, Users, Groups
Deactivate and delete your ROOT account credentials, it shouldn't be used or shared.
Create users instead.
**Users** can be part of multiple groups.
**Groups** only contain users, not other groups.
![iam-users-and-groups.png](iam-users-and-groups.png)

### Policies
Users and Groups can be assigned JSON documents, a.k.a IAM Policies:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "FullAccess",
            "Effect": "Allow",
            "Action": ["s3:*"],
            "Resource": ["*"]
        },
        {
            "Sid": "DenyCustomerBucket",
            "Action": ["s3:*"],
            "Effect": "Deny",
            "Resource": ["arn:aws:s3:::customer", "arn:aws:s3:::customer/*" ]
        }
    ]
}
```

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "dynamodb:DeleteItem",
                "dynamodb:GetItem",
                "dynamodb:PutItem",
                "dynamodb:UpdateItem"
            ],
            "Resource": [
                "arn:aws:dynamodb:us-west-1:123456789012:table/myDynamoTable"
            ],
            "Condition": {
                "ForAllValues:StringEquals": {
                    "dynamodb:LeadingKeys": [
                        "${cognito-identity.amazonaws.com:sub}"
                    ]
                }
            }
        }
    ]
}
```
These policies define the **permissions** of the users.
In AWS, you apply **the least privilege principle**:
don't give more permissions than a user needs.

Generate IAM user's keys for security tracing (but without access to IAM).
Create RBAC (Role Based Access Control).
Create Admin and attach roles for others.
Account ID -> Account Alias.
Account Alias must be globally unique.

IAM Policies inheritance:
<br>
Users inherit the policies from attached Groups.
<br>
Also, can `Inline Policy` be applied. 
`Inline Policy` — 1:1 mapping permissions only for 1 user / service and nobody else.
![iam-inheritance.png](iam-inheritance.png)

IAM Policy Structure:
<br>
`Version`: policy language version
<br>
`Id`: an identifier for the policy
<br>
`Statement`: one or more individual statements, which consists of:
<br>
`Sid`: an identifier for the statement
<br>
`Effect`: whether the statement allows or denies access (**Allow**, **Deny**)
<br>
`Principal`: account/user/role to which the policy applied to
<br>
`Action`: list of actions this policy allows or denies (based on the Effect)
<br>
`Resource`: list of resources on which actions will be applied
<br>
`Condition`: when to apply actions

Policies can be created using either JSON or Visual editor.

### IAM Multi-Factor Authentication (MFA)

Strong protection of your AWS account.
MFA = _password you know_ + _security device you own_

MFA device options in AWS:
<br>
Virtual device:
<br>
Google Authenticator (phone only)
<br>
Authy (multi-device)

Universal 2nd Factor (U2F) Security Key:
YubiKey by Yubico (3rd party) and other keychains.

### Accessing the AWS
To access AWS, you have 3 options:
1. Management Console (protected by password + MFA)
2. Command Line Interface (CLI) (protected by access keys)
3. Software Development Kit (SDK) (protected by access keys)

With CLI, output format can be `text`, `json`, or `yaml`.
AWS CloudShell — cloud terminal, where you can run your CLI commands,
upload and download files.

Users manage their own access keys.
Access Key ~= username
Secret Key ~= password

Configuration stored in `.aws/config`.
Credentials are stored in `.aws/credentials`.
You can give a name for your account with profile alias.
In `.aws/credentials` you can specify [$profile] node on top of `ACCESS_KEY` and `SECRET_KEY`.
In AWS CLI you can choose the profile using `--profile`.

Each service has permissions.
You can assign them to AWS services using policies.
For example, Inline Policy —
1:1 mapping permissions only for 1 user / service and nobody else.

### IAM Roles for Services
Some AWS service will need to perform actions on your behalf.
To do so, we need to assign **permissions** to AWS services (trusted entity) with IAM roles.
Most common use-cases are EC2 and Lambda.

Permissions, which are not explicitly allowed -> they are **implicitly denied**.
Everything can be setup in the IAM dashboard.

### IAM Security Tools
<br>
IAM Credentials Report (account-level) -
a report that lists all your account's users and the status of their various credentials.
<br>
IAM Access Advisor (user-level) -
shows the service permissions granted to a user and when those services were last accessed.
You can use this information to revise your policies.

### Shared Responsibility Model for IAM
AWS is responsible for Infrastructure (global network security),
configuration and vulnerability analysis, and compliance validation.

While, you are responsible for managing Users, Groups, Roles, Policies management, and monitoring.
Also, MFA enabling, key rotation, usage of IAM tools for applying appropriate permissions,
analyze access patterns and review permissions.

### IAM Summary

**Users**: mapped to a physical user, has a password for AWS Console
<br>
**Groups**: contains users only
<br>
**Policies**: JSON document that outlines permissions for users or groups
<br>
**Roles**: for AWS services, such as EC2 or Lambda
<br>
**Security**: MFA + Password Policy
<br>
**AWS CLI**: manage your AWS services using the command-line
<br>
**AWS SDK**: manage your AWS services using a programming language
<br>
**Access Keys**: access AWS using the CLI or SDK
<br>
**Audit**: IAM Credential Reports and IAM Access Advisor

## AWS Budget

In AWS, you can create budgets for an account and track billing per service.

## AWS EC2

Elastic Compute Cloud (EC2), Infrastructure as a Service.
It mainly consists of:
1. Renting virtual machines (EC2)
2. Storing data on virtual drives (EBS)
3. Distributing a load across machines (ELB)
4. Scaling the services using an auto-scaling group (ASG)

EC2 sizing and configuration options:
1. OS: Linux, Windows, macOS
2. CPU
3. RAM
4. Storage: EBS (Elastic Block Storage), EFS (Elastic File Storage), hardware (EC2 Instance Store)
5. Network: speed of the card, Public IP address
6. Firewall rules: security group 
7. Bootstrap script (on machine launch): EC2 User Data

Each EC2 User Data script runs from root user (`sudo` required).

`t2.micro` (1 vCPU, 1 GB) is part of the AWS free tier (up to 750 hours per month).

### EC2 Configuration

1. Amazon Machine Image (**AMI**) - is a template that contains the software configuration
   (operating system, application server, and application) required to launch your instance.
   Base image can be: Amazon Linux, Ubuntu, Windows, macOS, Red Hat, etc.
2. Instance type
3. Key pair for SSH connection
4. Storage volumes
5. Advanced: Spot instances, Tenancy, User Data script, etc.

After EC2 instance stops, Public IP can be **resigned**.
`0.0.0.0/0`- CIDR block of 'everywhere.'

### Instance Types

AWS EC2 Instance Types:
1. **General Purpose**, Great diversity of workloads such as web servers or code repos.
   Balance between: Compute, Memory, Networking.
2. **Compute Optimized**, Great for compute-intensive tasks that require high-performance processors:
   Batch processing workloads, Media transcoding, High performance computing (HPC),
   Scientific modeling, Machine Learning, Dedicated gaming servers.
3. **Memory Optimized**, Fast performance for workloads that process large data sets in memory.
   Use-cases: High performance databases, Distributed web scale cache stores,
   In-memory databases optimized for BI (business intelligence), Real-time processing of big unstructured data.
4. **Storage Optimized**, Great for storage-intensive tasks that require high,
   sequential read and write access to large data sets on local storage.
   Use-cases: High frequency online transaction processing (OLTP) systems,
   Relational and NoSQL databases, Cache for in-memory database,
   Data warehousing applications, Distributed file systems.

AWS has the following naming convention: `m5.2xlarge`, where


m: instance class
<br>
5: generation (AWS improves them over time)
<br>
2xlarge: size withing the instance class

### Security Groups

Security groups control how traffic is allowed into or out of our EC2 instances.
Security groups only contain `allow` rules.
Security groups rules can be reference by IP or by security group.

Security groups are acting as a 'firewall' on EC2 instances.
They regulate port accessing, IP ranges (IPv4 and IPv6),
inbound network (from others to the instance),
outbound network (from the instance to others).

Any **timeout is cause of EC2 Security Groups**. 

![ec2-security-groups.png](ec2-security-groups.png)

Security groups can be attached to multiple instances.
By default, all inbound traffic is `blocked` by default.
By default, all outbound traffic is `authorised` by default.

Referencing other security groups:
![ec2-security-groups-ref.png](ec2-security-groups-ref.png)

Classic Ports to know:
<br>
22 = SSH (Secure Shell) - log into a Linux instance
<br>
21 = FTP (File Transfer Protocol) - upload files into file share
<br>
22 = SFTP (Secure File Transfer Protocol) - upload files using SSH
<br>
80 = HTTP - access unsecured websites
<br>
443 = HTTPS - access secured websites
<br>
3389 = RDP (Remote Desktop Protocol) - log into a Windows instance

### EC2 Connection

Using SSH:

```shell
ssh -i certi.pem ec2-user@<public ip>
```

If you have an error: `UNPROTECTED PRIVATE KEY FILE` -> 

```shell
chmod 0400 certi.pem
```

Using EC2 Instance Connect:
In the Management Console, you can start a new browser session with connection to your EC2.

### EC2 Instances Purchasing Options

**On-Demand Instances** — short workload, predictable pricing, pay by uptime second.
Pay for what you use:
1. Linux or Windows — billing per second, after the first minute
2. All other OSs — billing per hour
Has the **highest cost but no upfront payment**, no long-term commitment.

**Reserved** (1 and 3 years):
   1. Reserved Instances — long workloads, you specify `Instance Type`, `Region`, `Tenancy`, `OS`.
   2. Convertible Reserved Instances — long workloads with flexible instances,
      you can change the `Instance Type`, `Region`, `Tenancy`, `OS`,
      less discount comparing to Reserved Instances.
Good for databases.
**Savings Plan** (1 and 3 years) — commitment to and amount of usage, long workload.
Get a discount based on long-term usage (up to ~72%),
usage beyond EC2 Savings Plan is billed at the On-Demand price.
Locked to a specific instance family & AWS region (e.g., `M5` in `us-east-1`).
But flexible across: Instance size (e.g., `m5.xlarge`, `m5.2xlarge`), and OS (e.g., Linux, Windows).

**Spot Instances** - short workloads, cheap, can lose instances (less reliable).
Can get a discount of up to 90% compared to On-Demand.
Instances that you **can 'lose'** at any point of time
if your max price is less than the current spot price.
**Useful for workloads that are resilient to failure.**
**Not suitable for critical jobs or databases.**
<br>
**Dedicated Host** - book an entire physical server, control instance placement.
A physical server with EC2 instance capacity fully dedicated to your use.
Allows you to address compliance requirements and use your existing server-bound software licenses.
Can be purchased using these options: On-Demand, Reserved.
**The most expensive** option.
<br>
**Dedicated Instances** - no other customers will share your hardware.
Instances **run on hardware that's dedicated to you.**
May share hardware with other instances in the same account.
No control instance placement (can move hardware after Stop / Start).
<br>
**Capacity Reservations** - reserve capacity in a specific AZ for any duration.
You can reserve it with a full price, even you don't stay in it.

### EC2 Instance Storage Options

#### EBS

Elastic Block Store (EBS) Volume
is a **network drive** you can attach to EC2 instances while they run.
It allows instances to persist data, even after their termination.
They can only be **mounted to one instance at a time**.
They are **bound to specific AZ**.

EBS Delete on Termination attribute:
by default, enabled in the EBS Root, and disabled in any other attached EBS.
It can be controlled by the AWS Management Console/AWS CLI.

**EBS Snapshots — a backup of the EBS volume** at a point in time.
Not necessary to detach volume to do snapshot, but recommended.
You can copy snapshots across AZ or Region.

EBS Snapshots Features:
1. EBS Snapshot Archive: move a snapshot to an 'archive tier' that is 75% cheaper,
   but takes within 24 to 72 hours for restoring the archive.
2. Recycle Bin for EBS Snapshots: setup retention rules (from 1d to 365d) to retain deleted snapshots,
   so you can recover them after an accidental deletion.
3. Fast Snapshot Restore (FSR): force full initialization of snapshot to have no latency on the first use,
   expensive $$$.

![ebs-snapshots-features.png](ebs-snapshots-features.png)

EBS volumes are network drives with 'limited' performance.
**If a high-performance disk is required, use EC2 Local Instance Store**.
EC2 Local Instance Store loses their storage if they are stopped (ephemeral storage).
EC2 Local Instance Store can't be used as a durable, long-term place to store data.
Good for buffer, cache, scratch data, temporary content.
Risk of data loss if hardware fails.
**Backups and Replication are your responsibility**.

EBS Volume Types:
1. **gp2/gp3 (SSD)**: General Purpose SSD volume
   that balances price and permanence for a wide variety of workloads.
2. **io1/io2 (SSD)**: Highest-performance SSD volume for mission-critical
   low-latency or high-throughput workloads.
3. **st1 (HDD)**: Low cost HDD volume designed for frequently accessed, throughput-intensive workloads.
4. **sc1 (HDD)**: Lowest cost HDD volume designed for less frequently accessed workloads.

EBS Volumes are characterized in `Size` | `Throughput` | `IOPS` (I/O Operations Per Second).
**Only gp2/gp3 and io1/io2 can be used as boot volumes (SSD).**

General Purpose SSD:

`gp2`: Small gp2 volumes can burst IOPS to 3,000,
**Size of the volume, and IOPS are linked**, max IOPS is 16,000,
3 IOPS per GB, means at 5,334 GB we are at the max IOPS.

`gp3`: Baseline of 3,000 IOPS and throughput of 125 MB/s, 
gp3 can increase IOPS up to 16,000 and throughput up to 1000 MB/s **independently**.

Provisioned IOPS (PIOPS) SSD:
Critical business application with sustained IOPS performance.
Or application **requires more than 16,000 IOPS**.
Great for **database workloads** (sensitive to storage performance and consistency).
Then, `io1/io2` is solution: size is 4GB - 16TB, max **PIOPS 64,000 for Nitro EC2 instances** and 32,000 for other,
**io1/io2 can increase PIOPS independently of storage size**,
io2 has more durability and more IOPS per GB (at the same price as io1).

io2 Block Express (4GB - 64 TB): sub-millisecond latency,
max PIOPS: 256,000 with an IOPS:GB ratio of 1,000:1,
**supports EBS Multi-attach**.

Hard Disk Drives (HDD):
cannot be a boot volume,
from 125GB to 16TB.

`st1`: Throughput Optimized HDD, great for Big Data, Data Warehouses, Log processing,
max throughput: 500 MB/s, max IOPS: 500.

`sc1`: Cold HDD, for data that is infrequently accessed, scenarios where the lowest cost is important,
max throughput: 250 MB/s, max IOPS: 250.

EBS Multi-attach: attach the same EBS volume to multiple EC2 instances in the same AZ.
Each instance has full read and write permissions to the HP volume.
**Up to 16 EC2 Instances at a time.**

Use-case:
1. Achieve **higher application availability** is clustered Linux applications.
2. Applications must manage concurrent write operations.

#### EFS

Elastic File System (EFS) — managed Network File System (NFS) that **can be mounted on many EC2**.
**EFS works with EC2 instances in multi-AZ.**
Highly available, scalable, expensive (3x gp2), pay per use.

Use-case:
1. Content management
2. Web serving
3. Data sharing
4. WordPress

**EFS is only compatible with Linux based AMIs.**
Encryption at rest using KMS.
POSIX file system that has a standard file API.
File system **scales automatically**, pay-per-use, **no capacity planning**.

EFS Classes:

1. EFS Scale
2. Performance Mode (set at EFS creation time)
3. Throughput Mode

EFS Storage Tiers:
1. Standard: for frequently accessed files 
2. Infrequent Access (EFS-IA)

EFS Availability and Durability Tiers:
1. Regional or Standard (old naming): Multi-AZ
2. One Zone
3. One Zone-IA (over 90% in cost savings)

#### EBS vs. EFS vs. Instance Store

EBS volumes:
1. can be attached to only one instance (expected io1/io2)
2. are locked at the AZ level
3. to migrate an EBS volume across AZ, we need to make a snapshot and restore it into another AZ
4. Root EBS volumes of instances get terminated by default if the EC2 instance gets terminated 

EFS:
1. it can be attached to many EC2 Instances
2. multi-AZ
3. only for Linux Instances (POSIX)
4. higher price point than EBS

Local Instance Store:
1. high-performance disk
2. lose their storage if they are stopped (ephemeral storage)
3. it can't be used as a durable, long-term place to store data

### AMI

Amazon Machine Image (AMI) is a customization of an EC2 instance **by reusing EC2 state**:
1. You add your own software, configuration, OS, monitoring, etc.
2. Faster boot/configuration time because **all your software is pre-packaged**

**AMIs are built for a specific region**, and can be copied across regions.

**Public AMI**: AWS provided.
<br>
**Your own AMI**: you make and maintain them yourself
<br>
**An AWS Marketplace AMI**: an AMI someone else made (and potentially sells)

![ami-cp.png](ami-cp.png)

## Load Balancing on AWS

Horizontal scaling implies distributed systems.
Using horizontal scaling, we need load balancing between them.
Scale out — increase number of instances.
Scale in - decrease number of instances.

High availability means running your application/system in at least 2 data centers (AZs).
The goal of high availability is to survive a data center loss.

Managing horizontal scaling on AWS:
1. Auto Scaling Group
2. Load Balancer

Managing high availability on AWS:
1. Run instances for the same application across multi AZ.

Why use a load balancer:
1. Spread a load across multiple downstream instances
2. Explore a single point of access (DNS) to your application
3. Health-checking instances
4. Separate public traffic from private traffic

### Elastic Load Balancer

An Elastic Load Balancer is a **managed load balancer**.

Types of load balancers on AWS:
1. **Classic Load Balancer** (CLB) — 2009, HTTP, HTTPS, TCP, SSL, **Deprecated**
2. **Application Load Balancer** (ALB) — 2016, HTTP, HTTPS, WebSocket
3. **Network Load Balancer** (NLB) — 2017, TCP, TLS, UDP
4. **Gateway Load Balancer** (GWLB) - 2020, IP Protocol (layer 3 of an OSI model)

some load balancers can be setup as internal (private) or external (public) ELBs.

EC2 instances should only **allow traffic only coming directly from load balancers**.

## AWS S3

AWS type of storages:
1. Amazon Elastic Block Storage -> General Purpose SSD, IOPS SSD, Throughput Optimized HDD, Cold HDD
2. Amazon Elastic File Storage
3. Object Storage -> S3 Standard, Standard-IA, One-Zone-IA, Glacier, Glacier Archive

AWS S3 use-cases:
1. Bucket + content
2. Web-site hosting
3. Data Lake
For enabling web-site hosting via S3, you need to expose `GetObject` permission to **public**.
With Data Lake use-case you can query objects.
Also, AWS S3 supports events.

## AWS DynamoDB

Item key consists of:
Partition key (Hash key);
or
Partition key (Hash key) and Sort key (Range key).

Local secondary index (LSI) —An index that has the same partition key as the base table, but a different sort key.

RCU - Read Capacity Units.
WCU - Write Capacity Units.

LSI — Local Secondary Index, **can be created only on table creation**, **up to 5** in scope of one table.
GSI — Global Secondary Index, can be created anytime, **up to 20** in scope of one table.

A global secondary index lets you query over the entire table, across all partitions.
The primary key of a global secondary index can be either simple (partition key) or composite (partition key and sort key).
A local secondary index lets you query over a single partition, as specified by the partition key value in the query.
The primary key of a local secondary index must be composite (partition key and sort key).

In general, you **should use global secondary indexes** rather than local secondary indexes.
The exception is when you need strong consistency in your query results,
which a local secondary index can provide but a global secondary index cannot
(global secondary index queries only support eventual consistency).

DynamoDB is NoSQL, serverless database.
You can do `Scan`, `Query`, or `GetItem` your Table **Items**.
`Scan` - iterates through all the items, consumes more RCUs.
`Query` - iterating require usage of indexes, consumes less RCUs.
`GetItem` - returns a set of attributes for the **item** with the **given primary key**.
If there is no matching item,
GetItem does not return any data and there will be no Item element in the response.

## AWS Lambda

## AWS API Gateway

## AWS Cognito

## AWS Cloudwatch

### XRAY
