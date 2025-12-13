## Cloud Computing Essentials

![[Cloud Computing]]


## Pricing

- AWS pricing fundamentals:
	- **Compute**
	- **Storage**
	- **Data transfer** - pay only for data transferred *out* of the cloud

## Shared Responsibility Model (SRM)

> [!quote]- Shared Responsibility Model
> Security and Compliance is a shared responsibility between AWS and the customer.
> ![Shared Responsibility Model](shared-responsibility-model.jpg)
> **Source**: [AWS](https://aws.amazon.com/compliance/shared-responsibility-model/) 

## Core Concepts

### Regions

- Cluster of data centers
- Most AWS services are region-scoped.

- **What determines where to launch a new service?**
	- ==Compliance== - Data never leaves a region w/o explicit permission
	- ==Customer Proximity== - Reduced latency
	- ==Service Availability== - Not all services are available in every region 
	- ==Pricing== - Varies from region to region

### Availability Zones (AZ)

- Within each region, there are several separate yet connected availability zones, usually anywhere from 3 to 6.
- Each AZ contains one or more data centers, each with redundant power, networking and connectivity.
- AWS Region: Sydney (ap-southeast-2)
	- ap-southeast-2a, ap-southeast-2b, ap-southeast-2c

### Points of Presence (Edge Locations)

- Deliver content to end users with low latency. 
- 400+ in 90+ cities across 40+ countries.

## Services

- Some global services - IAM, Route 53, CloudFront, WAF
- Some regional services - EC2, Elastic Beanstalk, Lambda, Rekognition

### IAM

- Identity and Access Management
- Root account is created by default and associated with an organization.
- Users
	- People within an organization 
	- Can be grouped
	- Can belong to multiple groups
	- Don't necessarily have to be part of a group
- Groups
	- Only contain users, not other groups
- Roles
	- Identities that can be assumed by users, applications, or AWS services
	- Used to grant temporary access to AWS resources without sharing long-term credentials.
	- Can have multiple policies attached to it, combining the permissions from each policy.
		- When an entity (user, application, or service) assumes a role, it gains the permissions defined by the policies attached to that role.
- Policies
	- JSON documents that define permissions
	- Assigned to users (Inline) or groups 
	- Attached to roles to define what the role is allowed to do
	- **Policy Structure**
		- `Version` - Policy language version (2012-10-17)
		- `Id` - Optional Policy identifier
		- `Statement` - One or more (required)
			- `Sid` - Optional statement identifier
			- `Effect` - '`Allow`' or '`Deny`' access
			- `Principal` - account/user/role to which the policy is applied
			- `Action` - list of allowed or denied actions
			- `Resource` - list of resources the actions apply to
			- `Condition` - Optional conditions that determine when the policy is in effect

```json
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Effect": "Allow",
			"Action": [
				"ec2:AttachVolume",
				"ec2:DetachVolume"
			],
			"Resource": "arn:aws:ec2:*:*:instance/*",
			"Condition": {
				"StringEquals": {"aws:ResourceTag/Department": "Development"}
			}
		}
		...
	]
}
```

> [!quote]- IAM Best Practices
> ![IAM Best Practices](iam-best-practices.png)
> - Credit: [Ultimate AWS Certified Cloud Practitioner](https://www.udemy.com/course/aws-certified-cloud-practitioner-new/)

- **Password Policies**
	- Min length
	- Whether changing own password is allowed
	- Character types required, 
	- Expiration date
	- Prevent re-use of passwords (that were used within a certain date range)

- **MFA Options**
	- Virtual MFA devices - Single device / multi-token
		- e.g. Authy
	- U2F - Single key / multi-user (root or IAM)
		- e.g. YubiKey
	- Hardware
		- Key fob devices

- **Ways to Access AWS**
	- **AWS Management Console (Web UI)** - Password + MFA
	- **AWS CLI** - Secret Access Keys
		- Alternative to using the management console web UI.
	- **AWS SDK** - Secret Access Keys
		- Language and platform-specific libraries
		- Embedded within applications to manage AWS services programmatically

> [!note]
> Users manage their own access keys.

- **IAM Roles for Services**
	- Permissions assigned to AWS services, so they can perform actions on user's behalf.
	- Common roles include EC2 roles, Lambda Roles, etc

- **IAM Security Tools**
	- **IAM Credentials Report** - Account-level
		- Lists all users and their credential status under an account
	- **IAM Access Advisor** - User-level
		- Lists service permissions granted to a user along with its last access time
		- Can be used to revise policies using the Least Priviledge approach

### Compute

#### EC2 (Elastic Compute Cloud)

- IaaS
	- Rent VMs (via EC2)
	- Data storage on virtual drives (via EBS)
	- Load distribution across machines (via ELB)
	- Service auto-scaling (via ASG)
- Can be configured to meet demands
	- OS
	- CPU & RAM
	- Storage Type and Amount
		- Network-attached (EBS / EFS)
		- Hardware (EC2 instance store)
	- Network Card
	- Firewall Rules
	- Bootstrap Script
		- Automated launch commands that run once (with root user permissions) on machine boot
			- e.g. Install updates/software, download files
- Instance types can be created that are optimized for different use cases 
	- Storage optimized
	- Network optimized
	- Memory optimized
	- Compute optimized
- General purpose instances provide a balance of the different resources. These are great for various types of workloads such as setting up web servers.
- An example name for an instance type can be `t2.micro`:
	- ==t== - instance class
	- ==2== - generation
	- ==micro== - size within instance class

- **Security Groups**
	- Fundamental to AWS network security.
	- 'Firewall' around EC2 instances to control inbound and outbound traffic.
		- Access to ports
		- Authorized IP ranges (IPv4 & IPv6)
	- Only contain allow rules
	- By default, 
		- all inbound traffic is blocked.
		- all outbound traffic is authorized (stateful).
	- Rules can be referenced by IP or by security group
	- Can be attached to multiple instances
	- Scoped to a region/VPC combination
	- If application
		- times out, the issue is because of a security group
		- refuses connection, the issue is because of an application or launch error

> [!important]
> It is considered best practice to maintain one separate security group for SSH access.
> 
> Never enter access keys into EC2 instance.

- **Common Ports**
	- 21
		- File Transfer Protocol (FTP)
		- Upload files
	- 22 
		- Secure Shell (SSH)
		- Log into a Linux machine
		- Also for Secure File Transfer Protocol (SFTP) - upload files using SSH
	- 80
		- HTTP
		- Access unsecure websites
	- 443
		- HTTPS
		- Access secure websites
	- 3389
		- Remote Desktop Protocol (RDP)
		- Log into a Windows machine

- **Purchase Options**
	- **On-Demand**
		- short-term, uninterrupted and unpredictable workloads
		- pay by the second (Linux / Windows)
		- pay by the hour (Other OS)
		- Highest cost with no upfront payment
		- No long term commitment
	- **Reserved**
		- Reserve specific instance attributes: type, region, OS, tenancy
		- Reservation period: 1 & 3 years
		- Payment can be made all upfront (cheapest), partially upfront or none upfront.
		- Reserved Instances (RI)
			- Long, steady workloads (e.g. databases)
			- Can be bought or sold in the RI Marketplace 
			- Scope: Regional or Zonal
		- Convertible Reserved Instances
			- Long workloads with flexible instances
			- Attributes can be changed
	- **Savings Plans**
		- 1 & 3 years
		- Long workload
		- Get a discount based on long-term usage
		- Commit to amount of usage (e.g. $X/hour for 1 or 3 years)
		- Usage past commitment is billed at On-demand pricing
		- Locked to a specific instance family and region
		- Flexibility across instance size, OS and tenancy
	- **Spot Instances**
		- Most cost-efficient
		- Short workloads resilient to failure
			- e.g. image processing, data analysis
		- Less reliable; Can be lost
	- **Dedicated Hosts**
		- Most expensive
		- Entire physical server
			- Visibility into low-level hardware
			- Targeted instance placement
			- Address strong regulatory or compliance needs
			- Useful for software with complicated licensing (BYOL)
		- Either on-demand or reserved
		- Per host billing
	- **Dedicated Instances**
		- Hardware not shared with others
			- May be shared with other instances on same account.
		- No control over instance placement
		- Per instance billing
	- **Capacity Reservations**
		- Reserved on-demand capacity in specific AZ for any duration
		- No time commitment or billing discounts
		- Short-term, uninterrupted workloads in a specific AZ
		- On-demand charge rate regardless of running instances

> [!question]
> Which option is suitable for a certain type of workload?

- **SRM for EC2**
	- The customer is responsible for 
		- OS patches and updates
		- Software and utilities installed on an instance
		- Security group rules, IAM roles and IAM user access

##### AMI (Amazon Machine Image)

- a customization of an instance: software, config, OS, monitoring
	- basically like a scaffold or template for an instance setup
- built for specific region but can be copied across.
- instances can be launched from:
	- A public AMI - AWS-provided
	- Custom AMI - custom made and maintained
	- Marketplace AMI - made/sold by a third-party

- **EC2 Image Builder**
	- used to automate the creation, maintenance, validation and testing of VMs or container images (AMIs)
	- can be run on a schedule

#### Container Services

##### ECS (Elastic Container Service)

- Launch Docker containers on AWS
- Self-provisioned and self-managed infra (EC2 instances)
- AWS takes care of starting and stopping containers.
- ALBs can be integrated
##### Fargate

- Serverless ECS
- AWS runs containers based on resource requirements (CPU/RAM)
##### ECR (Elastic Container Registry)

- Private Docker registry on AWS where images are stored to be run by ECS or Fargate

#### Serverless Services

- No server provisioning / management
- e.g. S3, DynamoDB, Fargate
##### Lambda

- Event-driven virtual functions that are time and runtime limited
	- short executions
- On demand and auto-scaling
- Multi-language support
- Pricing calculated per request and compute time
- **Lambda Container Images** - Lambda-specific way to run containers
	- Lambda is not ideal for generic Docker images

##### Amazon API Gateway

- Expose and proxy request to Lambda functions
- Fully managed, serverless and scalable
- support RESTful and WebSocket APIs
- support security, auth, API keys, API throttling, monitoring

##### AWS Batch

- Batches are 
	- jobs with a start and an end.
	- defined as Docket images and run on ECS
- AWS Batch provides managed batch processing at scale
- dynamically launch EC2/Spot instances with right amount of compute
- user schedules jobs, AWS takes care of the rest.
- no time limit
- support any runtime

##### LightSail

- Virtual servers, storage, databases and networking.
- low and predictable pricing
- simpler alternative to other services
	- for people with little cloud experience
- highly available, auto-scaling
- limited AWS integrations

### Storage

#### S3 (Simple Storage Service)

- allows storing objects (files) in buckets (directories)
- buckets 
	- must have a globally unique name (lowercase and numbers)
	- are ==created at the region level==
	- no concept of directories within buckets
- objects have a key which represents the full path of the object.
	- keys - made up of prefix and object name
		- e.g. `s3://my-bucket/dir1/.../image.png`
- max file size allowed - 5TB
- when uploading if file size exceeds 5GB, must use 'multipart upload'
- Use cases
	- Backup and storage
	- disaster recovery
	- archive
	- hybrid cloud storage
	- application hosting
	- media hosting
	- data lakes and big data analytics
	- software update delivery
	- static websites (if public access is enabled)

- **Security**
	- **User-based** - IAM policies
	- **Resource-based**
		- **Bucket Policies**
			- most common
			- bucket-wide rules
			- used to grant 
				- cross-account access
				- public access to bucket
				- force object encryption on upload
			- JSON-based
				- resources - buckets/objects
				- effect - allow/deny
				- actions - APIs allowed or denied
				- principal - account/user to which the policy applies
				- Public access is blocked by default. For public access policies to work, it needs to enabled.
		- **Object Access Control List**
			- fine-grain control
			- can be disabled
		- **Bucket Access Control List** (less common)
			- can be disabled
	- **Encryption**
		- Server-Side Encryption - default
		- Client-Side Encryption

> [!note]
> An IAM principal can access an S3 object if:
> - user IAM policy ALLOWs it or resource policy ALLOWs it, AND
> - there is no explicit DENY

- **Versioning**
	- considered best practice
		- unintended deletion protection
		- easy rollbacks
	- can be enabled at the bucket level
	- same key overwrite changes the version number

> [!note]
> Files unversioned prior to enabling versioning will have a version ID of null.
> 
> Suspending versioning doesn't delete previous versions.
> 
> Deleting a file version automatically rollback it back to its previous version.

- **Replication**
	- can be of two types:
		- Cross-Region Replication
			- useful for compliance, low latency access and cross-account replications
		- Same Region Replication
			- useful for log aggregation
	- versioning must be enabled in source and destination buckets
	- buckets can be in different accounts
	- copying is asynchronous
	- proper IAM permissions must be given to S3
	- version IDs are also replicated
	- replication rules apply to objects created/uploaded after the creation of the rule unless one-time replication is enabled during rule creation

##### Storage Classes

- **Durability**
	- Extremely high durability of objects across multiple AZs
	- Same across all storage classes
- **Availability**
	- depends on storage class
		- e.g. Standard is not available 53 minutes/year

###### Amazon S3 Standard

- Frequently accessed data
- low latency, high throughput
- able to sustain two concurrent facility failures
- **Use Cases**: big data analytics, mobile/gaming apps, content distribution

###### Amazon S3 Standard Infrequent Access (IA)

- lower cost
- infrequently but rapidly accessed data
- **Standard IA**
	- 99.99% availability
	- used for backups and disaster recovery
- **One Zone IA**
	- 99.5% availability
	- high durability in a single AZ
		- data is lost if AZ is destroyed
	- used for secondary backups and recreatable data

###### S3 Glacier

- low cost
- cost for storage and object retrieval
- used for archival and backups
- encryption auto-enabled
- **Instant Retrieval**
	- milliseconds retrieval
	- great for once every quarter
	- minimum storage duration of 90 days
- **Flexible Retrieval**
	- minimum storage duration of 90 days
	- Expedited - 1 to 5 minutes
- **Deep Archive**
	- long term
	- minimum storage duration of 180 days
	- Standard - 12 hrs
	- Bulk - 48 hrs

###### S3 Intelligent Tiering

- small monthly monitoring and auto-tiering fee
- automatically move objects automatically b/n Access Tiers based on usage
- no retrieval charges

- **Frequent Access Tier** - (auto) default
- **Infrequent Access Tier** - (auto) objects not accessed for more than 30 days
- **Archive Instant Access Tier** - (auto) objects not accessed for more than 90 days
- **Archive Access Tier** - (optional) objects not accessed between 90 and 700+ days
- **Deep Archive Access Tier** - (optional) objects not accessed between 180 and 700+ days

> [!note]
> Lifecycle rules define accounts to move S3 objects between different storage classes.

##### SRM for S3

- **AWS is responsible for:**
	- sustaining concurrent loss of data in 2 facilities
	- compliance validation
- **Customer is responsible for:**
	- versioning, policies, replication setup
	- logging and monitoring
	- data encryption at rest/in transit

#### EBS (Elastic Block Store)

- block-level storage
- network drive that can be (optionally) attached to instances while running
- because they are network-attached
	- there might be latency
	- they can be attached/detached quickly
- data is persisted even after termination
	- can be configured using the "delete on termination" attribute
		- can be managed via the console or CLI
		- enabled by default for root EBS
		- disabled by default for other EBS
- can only be mounted to one instance at a time
- are bound to a specific AZ
	- can be moved (backed up and restored) to a different AZ 
- provisioned capacity
	- billing occurs for all capacity purchased

- **Snapshots**
	- backups made at a certain point in time, so they can be restored
	- recommended (but not required) to detach volumes before a snapshot
	- can be copied across AZs or regions
	
	- **Snapshot Archive** - cheap, but take longer to restore (24-72 hrs)
	- **Recycle Bin** - rules can be set up to retain/retrieve deleted snapshots. 
		- retention period can be specified (1d - 1yr).

#### EC2 Instance Store

- block-level storage
- high-perf hardware disk
- better I/O perf and throughput
- ephemeral
	- if hardware fails, there is risk of data loss
	- use cases - buffer, cache, temporary data
	- backing up is the responsibility of the customer

#### EFS (Elastic File System)

- cross-AZ, cross-VPC and cross-Region, managed network FS (NFS)
- can be mounted (shared) across hundreds of instances
- works with a Linux instance
- highly available, scalable and expensive
- no capacity planning - pay per usage

- **EFS-IA (EFS Infrequent Access)**
	- lower cost than standard EFS (92% lower)
	- cost optimized for infrequently accessed files
	- files can be moved to EFS-IA based on last access time
	- enabled with a Lifecycle Policy
		- e.g. move files not accessed before 90 days to EFS-IA

#### SRM for EC2 Storage

- AWS responsible for 
	- replication of EBS/EFS data
- Customer responsible for 
	- data encryption 
	- backup procedures for EC2 instance store 

#### Amazon FSx

- launch fully-managed, third-party FS on AWS

- **FSx for Windows File Server (WFS)**
	- Windows-native shared FS
	- built on WFS
	- support SMB protocol and Windows NTFS
	- integrated with Microsoft Active Directory
	- can be accessed from AWS or on-premises infra
- **FSx for Lustre**
	- for high-perf computing (HPC)
		- ML, analytics, video processing

#### AWS Snow Family (SF)

- offline
- highly secure, portable devices 
- used for 
	- Edge Computing (to collect and process data at the edge)
		- long-term - 1-3 yrs discounted pricing
	- Data Migration (to migrate data into/out of AWS)

- **Snowmobile**
	- EBs of data ($10^6$ TB)
	- each has 100PB capacity
	- secure, temperature controlled, GPS and video surveillance
- **Snowcone**
	- smaller (batteries and cables not included)
	- TBs of data
	- offline or online (AWS DataSync)
	- edge computing
	- storage
	- transfer
- **Snowball Edge**
	- offline and up to PBs of data
	- large data cloud migrations
	- decommissioned data centers
	- disaster recovery
	- recurring transfers

- **OpsHub** - software for managing SF devices

#### Storage Gateway

- way to expose/bridge S3 data on-premise
- encryption auto-enabled
- used for disaster recovery, backup/restore, tiered storage
- **Types**: Tape, File and Volume

### Networking

#### Load Balancing

- Vertical Scalability (VS)
	- Scale up/down
	- increase size of instances
		- e.g. t2.micro -> t2.large
	- common for non-distributed systems such as databases
	- there is a limit to VS because of hardware capabilities
- Horizontal Scalability (HS) - Elasticity
	- Scale in/out
	- ASG, ELB
	- increase # of instances / systems
	- common on web applications
	- implies distributed systems
- High Availability (HA)
	- usually goes hand in hand with HS
	- running instances/systems in multiple AZs (at least 2) 
	- ASGs + ELBs in multi-AZ mode
	- Goal - survive a disaster such as loss of a data center

> [!note]
> Scalability is linked to but different from high availability (HA)

- Elasticity
	- cloud-native term referring to load-based auto-scaling
	- cost-optimized - pay per usage
	- matches demand
- Load Balancers (LB)
	- are servers that forward internet traffic to multiple instances
	- spread across multiple instances
	- expose a single point of access to apps
	- handle failure of instances
	- do regular health checks on instances
	- provide HTTPS
	- HA across AZs

- **ELB (Elastic Load Balancers)**
	- fully-managed LBs
- **ALB (Application Load Balancers)**
	- HTTP/HTTPS/gRPC protocols
	- Layer 7
	- Static DNS / URL
	- HTTP routing
- **NLB (Network Load Balancers)**
	- ultra high-perf (millions of requests per second)
	- Static IP
	- TCP/UDP protocols
	- Layer 4
- **GWLB (Gateway Load Balancers)**
	- Layer 3
	- GENEVE protocol
	- route traffic to firewalls managed on instances
	- for intrusion detection / deep packet inspection
- **Classic Load Balancers**
	- Retired 2023
	- Layer 4 & 7

#### ASG (Auto-Scaling Groups)

- allow scaling as load changes
	- scale in (remove instances) for low loads
	- scale out (add instances) for high loads
- ensure there is a min and max # of machines running
- auto-register/de-register instances to an LB
- replace unhealthy instances
- cost optimized

- **Scaling Strategies**
	- Manual
	- Dynamic
		- *Simple*
			- e.g. CPU usage < 30%, remove instances
		- *Target Tracking*
			- e.g. Avg. ASG CPU should stay at 40%
		- *Scheduled*
			- anticipated based on known usage patterns
			- e.g. upcoming concert ticket release date/time, increase min capacity to 10 at 6pm on Aug 10
		- *Predictive*
			- use ML to predict future traffic
			- auto-provision # of instances in advance
			- useful for time-based load patterns

### Databases
#### Relational
##### RDS (Relational Database Service)

- managed SQL DB on AWS
- can't SSH into instances
- Online Transaction Processing (OLTP)
- free tier
- **Deployments**
	- Read Replicas
		- read scalability
		- upto 15 read replicas
		- write done to main DB
	- Multi-AZ
		- data read/written on main DB
		- can have one other AZ as failover for HA
	- Multi-Region
		- write done to main DB
		- great for disaster recovery
		- local reads - better performance
		- replication cost

##### Aurora DB

- AWS cloud optimized
	- better performance over MySQL / Postgres on RDS
- OLTP
- supports MySQL / Postgres
- more costly, but efficient
- no free tier

#### Non-Relational (NoSQL)

- Flexible, horizontally scalable
- high performance
- **Types**: KV, Document, Graph, In-memory, Search
##### DynamoDB

- managed, serverless, NoSQL KV DB
- replication across 3 AZs
- fast, consistent, distributed, scalable and HA
- millions of req/sed, trillions of rows, 100s of TB of storage
- single digit ms latency retrieval
- integrated with IAM
- auto-scaling / low cost
- Standard & IA table class

- **DynamoDB Accelerator (DAX)**
	- fully managed in-memory cache for DynamoDB
	- microsecond latency

- ==**Global Tables**== - make a table accessible w/ low latency in multi-regions
	- **Active-Active Replication** - read/write to any region

##### ElastiCache

- managed Redis/Memcached
- in-memory DB - high perf, low latency
- useful for read-intensive workloads

##### Document DB

- implementation of MongoDB
- similar deployment concepts as Aurora
- managed, HA w/ replication across 3 AZs
- auto-scaling in storage (upto 64TB) & in workloads (millions of req/sec)

##### Neptune

- managed graph DB
- 3 AZ replication (HA),  15 read replicas 
- can store $10^9$ relations w/ ms latency
- used for recommendation engines, social network, fraud detection, knowledge graphs

#### Data Warehousing and Analytics

##### Redshift

- based on Postgres
- OLAP - online analytical processing
- analytics and data warehousing
- load data once every hour
- performant, HA and scalable (PBs of data)
- ==columnar== data storage (no rows)
- has SQL interface and BI tool integrations
- pay as you go

##### EMR (Elastic MapReduce)

- create Hadoop clusters (Big Data) to analyze/process vast amount of data
- made of hundreds of EC2 instances
- provisioned and configured
- auto-scaling
- used for data processing, ML, web indexing, big data

##### Athena

- Serverless query service to perform analytics against S3 objects
- used for BI, analytics, reporting, analyzing logs

##### QuickSight

- Serverless ML-powered BI service to create interactive dashboards
- integratable with AWS services (e.g. RDS, Aurora DB)
- used for analytics, visualizations, business insights

#### Specialized Databases

##### QLDB (Quantum Ledger DB)

- recording financial transactions
- 3 AZ replication (HA),  15 read replicas 
- managed & serverless
- review history of all changes made to your application data
- immutable
- manipulate data with SQL
- *not decentralized*

##### Managed Blockchain

- decentralized and managed service to
	- join public blockchain networks
	- create your own scalable private network

#### Database Tools & Services

##### Glue

- serverless and managed ETL (Extract, Transform & Load) service
- prepare data for analytics

#####  DMS (Database Migration Service)

- [ Source DB ] -> [ EC2 Instances Running DMS ] -> [ Destination DB ]
- Source DB remains available
- supports
	- homogeneous migration - e.g. Oracle -> Oracle
	- heterogeneous migration - e.g. MS SQL Server -> Aurora

#### SRM for Managed DBs

- **AWS**
	- monitoring, alerting, provisioning
	- OS patches
	- automated backup/restore, ops, upgrades
	- HA, VS, HS
- DBs run on EC2 instances have to be managed by customer.

### Automation & Management

#### Infrastructure as Code (IaC)

##### CloudFormation

- IaC
- Declarative way of outlining AWS infra, for any resource.
- Creates resources in the right order with the exact configuration specified.
- e.g. template
	- 2 EC2 instances
	- A security group
	- An S3 bucket
	- ELB
- Has support for almost all resources.
- Helps with productivity because developers can leverage existing templates.
- Predictable pricing / strategic pricing.

##### AWS CDK (Cloud Development Kit)

- Used to define infra using a familiar programming language, which get compiled to a Cloudformation template (JSON/YAML).
- Deploy infra and app runtime code together.
	- Lambda functions
	- Docker containers in ECS

#### Application Deployment

##### Elastic Beanstalk

- PaaS
- Developer-centric view of deploying apps on AWS.
	- The developer is responsible for the app code.
- Manage health-monitoring, configurable.
- Has 3 Architecture Models
	- Single Instance Deployment - Development
	- LB and ASG - Production or pre-production web apps
	- ASG-only - Production non-web apps (Workers)

##### AWS CodeDeploy

- Auto-deploy different versions of apps.
- Hybrid - Works with EC2 instances or on-prem instances.
- Servers must be provisioned / configured ahead of time.

#### CI/CD Pipeline

##### AWS CodeCommit

- Host git-based repos
- Versioned

##### AWS CodeBuild

- Code building service
- Compile -> Run Tests -> product Deploy-Ready Packages
- Serverless and fully managed
- Scalable, HA & secure
- Pay only for build time

##### AWS CodePipeline

- Basis for [[CI & CD|CI/CD]]
- Orchestrate different steps to auto-deploy code.

##### CodeArtifact

- Secure artifact management - storing and retrieving code dependencies

##### CodeStar

- Unified UI to manage development activities in one place.
- Edit code in the cloud using AWS Cloud9.

#### Development

##### Cloud9

- Cloud IDE for code collaboration and pair programming.

#### Systems Management

##### AWS SSM (Systems Manager)

- Helps you manage your EC2 & on-prem systems at scale.
- Hybrid, cross-OS
- Patching automation for enhanced compliance.
- Run commands across entire servers
- Store parameter config with SSM Parameter Store
- **SSM Session Manager** 
	- Start secure shell on servers without having SSH access (no port 22)
	- Can log session data

##### AWS OpsWorks

- Managed Chef & Puppet
- Alternative to AWS SSM
- Only provision standard AWS resources

### Global Apps 

- Decrease latency
- Disaster recovery
- Attack protection

#### Global Application Architecture

- **Single Region & AZ**
	- Not HA
	- Latency
	- Low difficulty
- **Single Region & Multi-AZ**
	- HA
	- Latency
	- Medium difficulty
- **Multi-Region, Active-Passive**
	- Services active in one region (r/w) and passive in another (r)
	- Moderate difficulty
	- Better global read latency
	- Bad global write latency
- **Multi-Region, Active-Active**
	- Better global r/w latency
	- More difficulty

#### DNS and Content Delivery

##### Route53

- Domain registration, health checks
- Managed DNS
	- Domain -> IPv4 - A record
	- Domain -> IPv6 - AAAA record
	- Hostname -> Hostname - CNAME
	- Hostname -> AWS resource - Alias
- Route users to closest deployment with least latency
- **Routing Policies**
	- *Simple* - no health checks
	- *Weighted* - load balanced
	- *Latency* - proximity
	- *Failover* - disaster recovery

##### Cloudfront

- CDN, great for static content
- Improved read performance
- 216 edge locations
- DDoS protection, Shield & AWS Web App Firewall integration
- Origins
	- S3 bucket (OAC + S3 policy) - distribute / cache files at the edge
	- Custom origin (HTTP)

- S3 Cross-Region Replication - great for dynamic content.

##### S3 Transfer Acceleration

- Upload files to S3 and transfer them to desired locations
- Provides faster uploads

##### Global Accelerator

- Improve global app availability and performance
- Proxy
- Leverage AWS internal network to optimize the route to application

#### Edge Computing

##### Outposts

- Server racks setup
- Managed by AWS on-prem with AWS services pre-loaded.
- Customer responsible for physical security.
- Benefits
	- Low-latency access
	- Data residency
	- Easier migration
	- Fully managed

##### Wavelength

- 5G

##### Local Zones

- Place select AWS services closer to end users to run latency-sensitive apps
- Extend VPC to more locations - Extension of an AWS region

### Cloud Integration

- **Synchronous Communication (App to App)**
	- e.g. Buying service <-> Shipping service
	- Problematic for sudden spikes of interest
- **Asynchronous / Event-based (App -> Queue -> App)**
	- e.g. Buy -> Queue -> Ship
	- SQS - Queue model
	- SNS - Pub/Sub model
	- Kinesis - Real-time data streaming model

#### SQS (Simple Queue Service)

- Producers -> Queue -> Consumers
- Fully-managed, pull-based
- Used to decouple apps
- Scalable ($1 - 10^4$ messages/second)
- Retention period for messages - 4 to 14 days
- Messages deleted after read by consumers
- Consumers share reading work
	- Scale horizontally
- FIFO Queue - processed in order

#### SNS (Simple Notification Service)

- Send message to many receivers
- Push-based
- Service -> SNS Topic -> (Service 1, Service 2, SQS Queue)
- Event publishers only send messages to one SNS topic
- As many event subscribers listen to SNS topic notifications
- Each subscriber to the topic will get all the messages

#### Amazon MQ

- When migrating to the cloud, use Amazon MQ.
- Managed message broker service for RabbitMQ and ActiveMQ (open protocols).
- Doesn't scale as much as SQS/SNS

### Monitoring & Observability

#### CloudWatch 

- **CloudWatch Metrics**
	- Provides metrics for every service in AWS
	- Metric - a variable to monitor
		- Timestamped
		- CPU utilization / Networking
- **CloudWatch Alarms**
	- Trigger automated notifications for any metric
	- States - OK, INSUFFICIENT_DATA, ALARM
- **CloudWatch Logs**
	- Enable real-time log monitoring
	- Create log agents on services (cloud or on-prem)
- **Amazon EventBridge**
	- formerly CloudWatch Events
	- React to events happening on an AWS account
	- Scheduled / event pattern

#### CloudTrail

- Provides governance, compliance and audit for your AWS account
- Enabled by default
- Get history of events made within an AWS account by console, CLI, SDK and AWS services
- Can be applied to all regions (default) or a single region
- Use cases
	- Looking up API calls
	- Investigate deleted resources
- **CloudTrail Insights** - automated analysis of CloudTrail Events

#### AWS X-Ray

- Visual analysis of apps in production
- Troubleshooting performance

#### CodeGuru

- ML-powered service for automated code reviews (CG Reviewer) (static code analysis) & app performance recommendations (CG Profiler) (runtime/production).

#### AWS Health Dashboard

- **Service History**
	- All regions, all services health
	- General status of AWS services
- **Your Account**
	- Provide alerts / remediation guidance when AWS is experiencing events that may impact customer
	- Personalized view into performance and availability of AWS services underlying customer's AWS resources (infrastructure)
	- Aggregated across organization

### Disaster Recovery

- **Backup & Restore** - Cheapest
- **Pilot Light**
- **Warm Standby**
- **Multi-site / Hot-site** - Most Expensive

- **Elastic Disaster Recovery**
	- Quickly and easily recover your physical, virtual and cloud-based servers into AWS

### VPC & Networking 

- **IP Addresses**
	- On EC2 instances, public IPv4 is ephemeral, while private IPv4 is fixed.
		- Public IPv4 can be made fixed using Elastic IP
			- Has ongoing cost if not in use
- **VPC (Virtual Private Cloud)**
	- Logically isolated sections of AWS
	- Regional, Span AZs
- **Subnets**
	- Partition network inside VPC
	- Single AZ resource
	- Public - accessible from the internet
	- Private - inaccessible from the internet
- **Internet Gateway**
	- Help VPC instances to connect directly to the internet
- **NAT Gateway (AWS-managed) & NAT Instances (Self-managed)**
	- Allow instances in private subnets to access the internet while private.
- **NACL**
	- Subnet-level firewall
	- Rules only include IP addresses
	- ALLOW & DENY rules
	- Stateless, inbound and outbound
- **Security Groups**
	- Instance-level firewall on ENI / EC2 instances
	- Only ALLOW
	- Rules include IP addresses and other security groups
	- Stateful
- **VPC Flow Logs**
	- Capture info about IP traffic going into interfaces: VPC, subnet, ENI flow logs
	- Help monitor & troubleshoot connectivity issues

#### VPC Connectivity

- **VPC Peering**
	- Connect 2 VPCs privately using AWS network
	- IP addresses range mustn't overlap
	- Not transitive

- **VPC Endpoints**
	- Help connect to AWS services using a private network instead of the public network
	- Enhanced security and low latency
	- *Endpoint Gateway* - for S3 & DynamoDB
	- *Endpoint Interface* - for other services

- **PrivateLink**
	- Privately connect to a service in a third-party VPC
	- Most secure and scalable way to expose a service to thousands of VPCs
	- Doesn't require VPC peering, NAT, route tables

- **Site-to-Site VPN**
	- Connect on-prem VPN to AWS (encrypted)
	- Over public internet
	- On-prem must use Customer Gateway
	- AWS must use Virtual Private Gateway

- **Direct Connect**
	- Establish physical connection between on-prem and AWS
	- Private, secure, fast
	- Over private network
	- More expensive

- **Client VPN**
	- Connect from PC using OpenVPN to a private network in AWS and on-prem
	- Connect to EC2 instances over a private IP
	- Over public internet

- **Transit Gateway**
	- Transitive peering between thousands of VPCs and on-prem, hub-and-spoke (star) connection

### Security & Compliance

#### SRM

- **AWS**
	- Security of the cloud and AWS-managed services
- **Customer**
	- Security in the cloud
	- Data encryption
- **Shared Control**
	- Patch management
	- Configuration management
	- Awareness & training

#### DDoS Protection

- **AWS Shield Standard**
	- All customers at no cost
- **AWS Shield Advanced**
	- 24/7 premium
	- EC2 & Elastic Beanstalk
	- More sophisticated attacks
	- 24/7 access to DDoS Response Team (DRP)
	- Protection from high fees from spikes
- **WAF**
	- Web App Firewall
	- Based on rules
	- Deploy on ALB, Cloudfront
	- Protection against common exploits
- **Cloudfront & Route53**
	- Combined with AWS Shield to provide attack mitigation at the edge
- **AWS Network Firewall**
	- Protect entire Amazon VPC from layer 3 to layer 7
	- Any form of traffic
- **Regeneration Testing**
	- Allowed on some services without approval from AWS, but not all

#### Encryption & Key Management

- **At Rest** - store/archived on a device
- **In Transit** - data being moved from one location to another over a network

- **Key Management Service (KMS)**
	- Keys managed by AWS, access can be managed by user
	- Can be opt-in or auto-enabled

- **CloudHSM (Cloud Hardware Security Module)**
	- AWS provisions dedicated encryption hardware
	- Keys are managed by the user

- **AWS Certificate Manager (ACM)**
	- Easily provision, manage and deploy SSL/TLS certificates
	- Supports both public and private TLS certificates
	- Provide in-flight encryption for websites

- **Secrets Manager**
	- Store secrets with ability to force rotation of secrets every X number of days
	- Integration with RDS
	- Encrypted with KMS

#### Other Tools

- **AWS Artifact**
	- Provides customers with on-demand access to AWS compliance docs and agreements used to support internal audit and compliance

- **GuardDuty**
	- Intelligent thread discovery to protect AWS account using ML
	- Can protect against cryptocurrency attacks

- **Inspector**
	- Automate security assessments
	- Only for EC2 instances (unintended network access, and known vulnerabilities), container images and lambda functions
	- Can be integrated with EventBridge
		- Package vulnerabilities - EC2, ECR and Lambda
		- Network reachability - EC2

- **Config**
	- Record configs/changes over time
	- Regional

- **Macie**
	- Fully managed data security and privacy service
	- Use ML and pattern matching to discover and protect sensitive data in AWS such as PII

- **Security Hub**
	- Central security tool to manage security across several AWS accounts and automate checks

- **Detective**
	- Analyze, investigate and identify the root cause of security issues or suspicious activities (using ML and graphs)

- **Abuse**
	- Report suspected AWS resources used for abusive or illegal purposes

- **IAM Access Analyzer**
	- Find out which resources are shared externally
	- Access outside Zone of Trust (ZoT) is reported

#### Root User Privileges

- Some actions can only be performed by root
	- Change account settings
	- Close account
	- Change/cancel support plan
	- Register as seller in the Reserved Instances Marketplace
	- Sign up for GovCloud

### Machine Learning

#### Vision and Speech

- **Rekognition**
	- Automate media analysis using ML

- **Transcribe**
	- STT (Speech-to-Text)
	- Automatic removal of PII using Redaction
	- Automatic language identification

- **Polly**
	- TTS (Text-to-Speech)

#### Language Processing

- **Translate**
	- Language translation

- **Amazon Lex**
	- Powers Alexa, conversational bots 
	- Speech and intent recognition

- **Comprehend**
	- NLP (Natural Language Processing)
	- Fully managed and serverless

#### ML Platforms

- **SageMaker**
	- Fully managed service to build ML models

#### Specialized ML Services

- **Forecast**
	- Fully managed service to deliver highly accurate data

- **Kendra**
	- Fully managed service for document search

- **Personalize**
	- Fully managed service to build apps with real-time personalized recommendations

- **Textract**
	- Auto-extract text from any scanned documents

#### Contact Center

- **Amazon Connect**
	- Virtual cloud contact center
	- Can integrate with CRM systems

### Account Management

#### AWS Organizations

- Manage multiple AWS accounts
	- Consolidated billing
		- Combined usage
			- Share volume pricing, reserved instances and saving plans discounts
		- One bill of all accounts
	- Aggregated usage price benefits
	- Pooling of reserved EC2 instances
- API available to automate AWS account creation
- Restrict account privileges using Service Control Policies (SCP)

- **SCP**
	- Whitelist/blacklist IAM accounts
	- Applied at the OU/Account level to all users and roles
	- Doesn't apply to the master account
	- Must have explicit allows
	- Restrict Access to Certain services
	- Enforce PCI compliance by explicitly disabling services
	- OU level takes precedence over account level access
		- Accounts inherit from OU

- **Control Tower**
	- Easy and automated way to set up and govern a secure, compliant multi-account AWS environment

- **AWS Resource Access Manager (AWS RAM)**
	- Share AWS resources you own with other AWS accounts (inside or outside of orgs)
	- Avoid resource duplication

- **Service Catalog
	- Quick self service portal for new users to launch a set of authorized products pre-defined by admins

#### Identity & Access

- **AWS STS (Secure Token Service)**
	- Create temporary, limited-privilege credentials to access AWS resources
	- Short-term credentials with expiration date configured

- **Cognito**
	- Provide identity for app users

- **AWS IAM Identity Center**
	- Formerly SSO
	- One login for all AWS accounts in org
	- Centrally manage access to multiple AWS accounts and business apps

#### Pricing & Billing

##### Pricing Models in AWS

- **Pay as You Go**
- **Save When You Reserve**
- **Pay Less by Using More**
- **Pay Less as AWS Grows**

- **Savings Plans**
	- Commit a certain dollar amount per hour for a certain duration (1 or 3 years)

##### Cost Management

- **Compute Optimizer**
	- Lambda, EBS, EC2, ASG
	- Reduce costs and improve performance by recommending optimal resources for workloads

- **Billing / Costing Tools**
	- **Estimate** - Pricing calculator
	- **Tracking**
		- Billing dashboard
		- Cost allocation tags
		- Cost and usage reports
		- Cost Explorer - forecast usage up to 12 months based on previous usage
	- **Monitoring**
		- Billing Alarms - for actual cost not projected
		- Budgets - create budget and send alarms when cost exceeds the budget
			- Types: Usage, Cost, Reservation, Saving Plans
	- **Cost Anomaly Detection**
		- Continuously monitor cost and usage using ML to detect unusual spending
	- **Service Quotas**
		- Notify when you're close to a service quota value threshold

> [!note]
> - Resource groups rely on tags.
> - Cloudfront pricing differs from region to region.
> - On-demand Linux EC2 - Pay per second

#### Trusted Advisor (TA)

- High-level AWS account assessment
- Provide recommendations on 5 categories:
	- Cost option
	- Performance
	- Security
	- Fault tolerance
	- Service limits
- **TA Support Plans**
	- 7 Core Checks (Basic & Dev Plans)
		- S3, 
		- Security Groups, 
		- IAM use, 
		- MFA on Root Account
		- EBS & RDS Public Snapshots
		- Service Limits
	- Full Checks (Business & Enterprise)
		- Above 5 categories
		- Ability to set CloudWatch Alarms when reaching limits
		- Programmatic access using AWS Support API
	- Business Support Plan - Most cost-effective option with 24/7 support

### Other Services

#### Amazon Workspaces

- Managed desktop as a service (DaaS) solution to easily provision Windows/Linux desktops.
- Eliminate management of on-prem virtual desktop infrastructure (VDI)
- Multi-region for reduced latency

#### AppStream

- Desktop app streaming service within a web browser

#### IoT Core

- Easily connect IoT devices to the AWS cloud
- Serverless, secure and scalable

#### Elastic Transcoder

- Convert media files stored in S3 into media files in the format required by the consumer playback devices

#### AppSync

- Makes use of GraphQL to store and sync data across mobile and web apps in real-time

#### Amplify

- Suite of services to develop and deploy scalable full-stack web and mobile apps

#### DeviceFarm

- Fully managed service that tests web and mobile apps against browser and real devices concurrently

#### AWS Backup

- Fully managed service to centrally manage and automate backups across AWS services (to S3)

#### DataSync

- Move large amounts of data from on-prem to AWS
- All replication tasks after first full load are going to be incremental

#### Application Discovery Service

- Plan migration projects by gathering info about on-prem data centers
- **Application Migration Service** - re-host solution which simplifies migrating apps to AWS

#### Migration Evaluator

- Build data-driven business case for migration to AWS

#### Fault Injection Simulator (FIS)

- Fully managed service for running fault injection experiments on AWS workloads (based on Chaos Engineering)

#### Step Functions

- Build serverless visual workflows to orchestrate Lambda functions

#### Ground Station

- Fully managed service for controlling satellite communications, process data and scale operations

#### Pinpoint

- Scalable two-way marketing communications service

## Architecture

#### Well Architected Framework (WAFr)

- Don't guess capacity need, use auto-scaling
- Test systems at production scale
- Design based on changing requirements
- Drive architecture using data
- **The 6 Pillars of WAFr**
	- **Operational Excellence**
		- Ops as Code / IaC (e.g. CloudFormation)
		- Annotate documentation
		- Make frequent, small, reversible changes
		- Refine operation procedure frequently
		- Anticipate and learn from failures
	- **Security**
		- Implement strong identity foundation based on least privilege
		- Enable traceability (via logging)
		- Apply security at all layers
		- Automate best practices
		- Protect data in transit and at rest
		- Prepare for incidents
	- **Reliability**
		- Test recover procedures
		- Scale horizontally
		- Stop guessing capacity
		- Automatically recover from failure
	- **Performance Efficiency**
		- Use serverless architecture
	- **Cost Optimization**
		- Adopt a consumption mode
		- Measure overall efficiency
		- Analyze / attribute expenditure
		- Use managed services to reduce cost
	- **Sustainability**
		- Understand your impact
		- Establish goals
		- Adopt new hardware and software offering
		- Use managed services
- **AWS Well-Architected Tool**
	- Review architectures against the 6 pillars and adopt architectural best practices

#### AWS Cloud Adoption Framework (CAF)

- Whitepaper
- Build and execute a comprehensive plan for digital transformation through innovative use of AWS
- Identifies original capabilities and groups them in 6 perspectives:
	- **Business Capabilities**
		- *Business* - ensure cloud investments accelerate digital transformation ambitions and business outcomes
		- *People* - serves as a bridge between tech and business
		- *Governance* - maximize original benefits and minimize risks
	- **Technical Capabilities**
		- *Platform* - build enterprise-grade, scalable hybrid cloud platform
		- *Security* - achieve confidentiality, integrity and availability of data
		- *Operations* - ensure cloud services are delivered at level that meet business needs
- **Right Sizing**
	- Match instance types and sizes to your workload performance and capacity requirements at lowest cost
	- Scaling is easy, so start small
	- Important to do before a cloud migration and continuously as needs change

#### Cloud Design Principles

- Scalable - in/out and up/down
- Automation
- Disposable resources
	- Easily disposable and configurable servers
- Loose coupling
	- Failure (or change) in one component doesn't cascade to others
- Think services, not servers

## Ecosystem

- **Marketplace** - Catalog of software listings from independent software vendors
- **Support** 
- **Training**
- **Professional Services and Partner Network (APN)**
	- APN Technology Partners
	- APN Consulting Partners
	- APN Training Partners
	- APN Competency Program
	- APN Navigate Partners

#### AWS IQ

- Quickly find professional help for your AWS projects

#### AWS re:Post

- Managed Q&A service/forum (public)
- Unanswered premium customer questions are passed onto AWS support engineers

#### AWS Managed Services

- A team of AWS experts who provide infrastructure and application support on AWS

---
## Further

### Books 📚

-  Amazon Web Services in Action (Andreas Wittig)

### Resources 🧩

- [AWS in Action (Playlist by Academind - YouTube)](https://www.youtube.com/playlist?list=PL55RiY5tL51rudermnWTq1LlGC1BL1g3l) ⭐

- [IAM Basics (CloudCasts)](https://cloudcasts.io/course/iam-basics)

- [Cloud Computing with AWS (Sam Meech-Ward)](https://www.sammeechward.com/playlists/cloud-computing) ⭐

### Videos 🎥

![The only Cloud services you actually need to know (YouTube)](https://www.youtube.com/watch?v=gcfB8iIPtbY)

![Easily Deploy Full Stack Node.js Apps on AWS EC2](https://www.youtube.com/watch?v=nQdyiK7-VlQ)

![Intro to IAM Roles and Policies on AWS - YouTube](https://www.youtube.com/watch?v=BSodkwWB-8s)
