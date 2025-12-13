## Intro

- automated and repeatable
- a culture of strong collab b/n Dev & Ops
- a set of practices aimed at improving time-to-market
- should work in harmony with Agile practices

## 4 Key Principles**

- ==Automation of the [[SDLC|Software Development Lifecycle]]==: This includes automating testing, builds, releases, and the provisioning of development environments to increase efficiency and reduce errors.
- ==Collaboration and communication==: DevOps encourages cross-functional teams to work together closely, share responsibilities, and communicate frequently to reduce inefficiencies and improve the quality of services provided to customers.
- ==Incremental development and rapid delivery==: DevOps focuses on incremental development and rapid delivery of software, allowing teams to respond quickly to customer needs and build competitive advantage.
- ==Monitoring and feedback loops==: DevOps practices include monitoring and logging to ensure the quality of application updates and infrastructure changes, allowing teams to reliably deliver at a more rapid pace while maintaining control and preserving compliance.

## Notes

- Some tasks in DevOps practices are shifted from IT to developers.
- DevOps can create new requirements, such as tooling support.
- The removal of oversight from deployments can create risk for organizations.
- Enabling DevOps practices often requires organizational change.
- Automation is not necessarily required to implement DevOps practices.
- Resource cost is important to DevOps practices

## Collaboration

- the most important aspect of DevOps practices
- shared responsibility b/n devs and ops personnel for incident handling
- consistent practices for both devs and ops personnel

## Practices

- During release, organizations care about:
	- collab with customers and stakeholders
	- packaging assets and service components
	- tracking deployment packages
	- can include automation to provide consistent feedback and deployments

## Why?

- Reduce errors
- Catch bugs before deployment
- Reproduceable deployment
- Faster code delivery
- Reduce money, time, customer costs that come with potential release failures
- Easier scaling due to automation
- Frequent releases
- Limited capacity of Ops staff
	- reduce the need for dedicated operations personnel
	- automating many of the tasks formerly done by operations
	- having developers assume a portion of the remainder
- Coordination between devs and ops personnel

---

- During a release process, organizations care about:
	- collaboration with customers and stakeholders,
	- packaging assets and service components,
	- tracking deployment packages,
	- Can include automation or IAC to provide consistent feedback and deployments
- Automation is key but not necessary. Tools and scripts also can enforce organization-wide policies.
	- Integration
	- Deployment
	- Provisioning
	- Configuration
- Automation will reduce the incidence of errors and will shorten the time to deployment.
- Purpose of automation tooling in DevOps: provide consistent feedback and deployments
- Source Code Management - Git
	- Branching / Merging
	- Commit early and often
- CI - process of automating code integration.
	- Merge, Build, Test
	- Connect to SCM repo, Pull, Build, Test, Approve merges, Notify of breakages
- CD
	- **Infra Provisioning** - Get servers spun up
	- **Infra Configuration** - Install requirements
	- If part of CI, utilizes CI tools. Otherwise, connects to artifact management tool which takes an existing artifact and deploys it.
- Infra as Cattle
	- Easily
		- Changed
		- Replaced
		- Duplicated
		- Automated
- Provisioning & Configuration
	- VMs
		- Emulate the entire system
		- Specific OS
		- Needs configuration
	- Containers
		- Cross-platform
		- Contains all needed components
		- Once configured, always configured
	- Config / Infra as Code:
		- Version controlled
		- Looks like code
- **Canaries** - deploy to small number of servers
- **Rollbacks** - how to decommission an update
- **Microservices** - small stateless portions of a system connected through a larger system

## Potential Barriers

- Tool support
- Personnel issues
- Organizational culture
- Organization / department type
	- Regulated Industry / Domain
	- Mature / Slow Moving Domains

## Concerns

- Security
	- Data loss
	- DevSecOps
- Getting Started
	- Infra to support tooling
	- Getting customers on board
	- **Goal** - Reduce the time between software development and deployment while maintaining quality.
	- **Lifecycle**
		- ![software-devops-lifecycle.png](/assets/images/devops.software-devops-lifecycle.png)
	- Notes on Agile
		- Roles in a team executing a DevOps process consist of service
		  owner, reliability engineer, gatekeeper, and DevOps engineer.

## The Cloud as a Platform

- Platform as a Service (PaaS)
	- Development Tools
	- Web Servers
	- Database
	- Execution Runtime
- Software as a Service (SaaS)
	- CRM
	- Virtual Desktops
	- Web-based email services
- Infrastructure as a Service
	- VMs
		- an emulation of a physical machine
		- are set in an environment, a set computing resources
		- **Elasticity** is the ability of resources to grow or shrink to meet demands
		- **Baking an image** refers to the amount of software pre-installed on a VM
		- Common causes for VM failure:
			- **stateless components** - recovery by creating another instance of the same VM image and ensuring that messages are correctly routed to it.
			- **client states**
			- **application states**
	- Storage
	- Load Balancers
	- Networks
- Environment: set of computing resources
- Dependability problems come from: possibility that an instance can fail, inter-team coordination, and correctness of environment
- **NoSQL (Not Only SQL) DBs**
	- not as mature as relational systems
	- appropriate data models must be decided upon by application programmer
	- applications may use multiple DB systems for d/t needs
- **DevOps Consequences of the Unique Cloud Features**
	- **Environments** - ensuring the environments meet the needs of the development and operations tasks
	- **Easy VM Creation** - controlling the proliferation of VMs
	- **Databases** - managing different database management systems

## Operations

- Things to consider when designing a service
	- What are the SLAs for the service?
	- What automation will be involved as a portion of the service?
	- Implications of the service?
	- What are the information security implications of the service?
	- What are the personnel requirements of the service?
	- What are the business continuity implications of the service?
- Responsibilities of Dev + Ops Personnel (Overlap)
	- Provisioning of hardware
	- Provisioning of software for organizational support
	- IT Functions: Service Desk Support, Technology Experts
	- Capacity Planning
	- Monitoring logs and other data in regards to app performance
	- Business continuity - Cost / Benefit analysis of:
		- data loss in the event of a failure
		- time it takes to recover from a failure
	- Information Security

## Overall Architecture

- While some DevOps practices are independent of architecture, in order to get the full benefit of others, architectural refactoring may be necessary.
- An organization can introduce continuous deployment without major architectural modifications.
- Microservices
	- composes several smaller services into a single system with complete functionality
	- coordination between dev teams responsible for interacting services is important.
	- multi-version deployments
	- reduces the necessity for inter-team coordination by making global architectural choices.
	- deployment rollbacks
	- **Dependability** - stateless services
		- **Problems**
			- small amount of inter-team coordination
			- environment correctness
			- possibility of a service instance failing
	- **Modifiability** - small services
- Continuous Deployment
	- Continual Service Improvement is the process and goal of improving the alignment of IT services with business needs and the quality of deployed software
	- Requires some architectural support.

## Automation

### Infrastructure as Code (IaC)

- Allows developers and operations teams to manage and provision infrastructure through code rather than manual processes.

- IaC uses declarative definitions to specify the desired state of infrastructure. 
	- Instead of writing step-by-step instructions, definitions describe what you want the end result to look like.
	- e.g. Using Terraform to create an AWS EC2 instance with specific attributes, without detailing the exact steps to create it:

```
resource "aws_instance" "web_server" {
	ami           = "ami-0c55b159cbfafe1f0"
	instance_type = "t2.micro"
	tags = {
		Name = "WebServer"
	}
}
```

- Files are treated like application code and stored in version control systems like Git.
	- This enables tracking of changes, collaboration, and rollbacks.
- IaC tools ensure idempotency.
	- The same configuration can be applied multiple times without changing the result beyond the initial application.
	- e.g. Using Ansible to Install Apache if it's not present, but don't make changes if already installed:

```yml
- name: Ensure Apache is Installed
	yum:
		name: httpd
		state: present
```

- IaC enables automation of infrastructure provisioning and management, reducing manual errors and increasing efficiency.
	- e.g. Using AWS CloudFormation to automatically create an S3 bucket when applied:

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Resources:
	MyS3Bucket:
		Type: 'AWS::S3::Bucket'
		Properties:
			BucketName: my-unique-bucket-name

```

- IaC promotes creating reusable modules for common infrastructure patterns.
	- e.g. Using Terraform modules to reuse a pre-defined VPC module, which allows for consistent VPC creation across projects:

```hcl
module "vpc" {
	source = "./modules/vpc"
	cidr_block = "10.0.0.0/16"
}
```

- IaC allows treating infrastructure like software, applying software development practices to infrastructure management. 
	- e.g. Using TypeScript with AWS CDK to define a VPC:

```ts
import * as ec2 from 'aws-cdk-lib/aws-ec2';
import { Stack, StackProps } from 'aws-cdk-lib';
import { Construct } from 'constructs';

class MyInfrastructureStack extends Stack {
	constructor(scope: Construct, id: string, props?: StackProps) {
		super(scope, id, props);

		const vpc = new ec2.Vpc(this, 'MyVPC', {
			maxAzs: 2
		});
	}
}
```
