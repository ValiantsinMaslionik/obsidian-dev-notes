#aws

---

[[zDOC_AWS_AWS-Starship-Diagram.pdf]]

# Security (Shields around the starship)

## Internal Network Security (Onboard security)

- **GuardDuty** - Real-time scanner
- **Inspector** - Scan for patches
- **CloudTrail** - Audit log
- **Security Hub** - Security dashboard
- **Macie** - Sensitive data scan
- **Secret Manager** - Use passwords in code
- **Detective** - Investigate breaches

## Internet Security (Shields)

- **Shield** - Protects against Distributed Denial of Service (DDoS) attacks, which can crash a service owing to a high volume of traffic.
		![[zIMG-AWS-ICON-Shield.svg]]
- **Web Application Firewall** (WAF) - WAF is a web application firewall that allows a user to manage access to his content and monitor HTTP(S) requests sent to an Amazon CloudFront distribution or an Amazon API Gateway, among other things.
		![[zIMG-AWS-ICON-WAF.svg]]
- **Security Group** - Internet line filter
- **Firewall Manager** - Is helpful in streamlining administration and maintenance responsibilities for a number of protections, such as AWS Network Firewall, WAF, AWS Shield Advanced and Amazon VPC security groups  etc.
		![[zIMG-AWS-ICON-FirewallManager.svg]]

## Access Controls (Airlock)

- **Identity Center** - Single sign on
- **Organizations** - multiple root accounts

## Identity Access Management (IAM)

- *IAM resources*
	- **IAM policy**
	- *IAM Identities*
		- **IAM Group**
		- *IAM Entities*
			- **IAM User**
			- **IAM Role**

# Software as a Service ([SaaS](https://www.techtarget.com/searchcloudcomputing/definition/Software-as-a-Service)) (Upper decks of the ship)

## Developer (Computer Lab)

- **AWS Software Development Kit** (SDK) - Programming libraries
- **X-ray** - Find bottlenecks
- **Command Line Interface** (CLI)

## DevOps (Bridge)

> **ML** - Machine Learning

- **CodePipeline** - Ties dev tools together
- **CodeCommit** - Like GitHub
- **CodeBuild** - Continuous integration (CI)
- **CodeDeploy** - Continuous deployment (CD)
- **DevOpsGuru** - ML monitoring
- **Elastic Container Registry** - Like Docker Hub
- **CloudWatch** - monitoring & log files
- **CloudFormation** - AWS infrastructure as code
- **OpsWork** - Control servers for Chief & Puppet
- **CodeGuru** - ML Code Review

## APIs (Flight control)

- **API Gateway** - Using the AWS global network, this AWS symbol allows you to design, publish, maintain, monitor, and protect APIs at any scale. A user can also run numerous versions of the same API at the same time.
		![[zIMG-AWS-ICON-ApiGateway.svg]]
- **AppSync** - [GraphQL](https://graphql.org/faq/getting-started/) with offline sync

## Machine Learning (Science Lab)

- **Comprehend** - Text analysis
- **Recognition** - Image classification
- **Textract** - Scanned documents
- **Personalize** - Product recommendations
- **SageMaker** - Run your own ML models
- **Lex** - Chatbots
- **Polly** - Text-to-voice
- **Transcribe** - Voice-to-text
- **Translate** - Multilingual

## User Authentication (Personnel Office)

- **Cognito** - User logins

# Platform as a Service ([PaaS](https://www.techtarget.com/searchcloudcomputing/definition/Platform-as-a-Service-PaaS)) (Middle of the starship)

## Code Platforms (Transporter Rooms)

- **Elastic Beanstalk** - Single app platform. It is used to automatically deploy an EC2 instance, load balancing, auto-scaling, and application health monitoring.
		![[zIMG-AWS-ICON-ElasticBeanstalk.svg]]
- **Lightsail** - Traditional web host. A user can have access to containers, storage, and databases at a lower price compared to EC2. It can be used to run simple web apps, or create any other form of small application. It can also be used to launch experimental tests.
		![[zIMG-AWS-ICON-LightSail.svg]]
- **Elastic Container Service** (ECS) - Thumbdrive containers. Container service that enables users to effortlessly build, manage, deploy and scale containerized applications. It is used to start, terminate, and manage clustered containers.
		![[zIMG-AWS-ICON-ECS.svg]]
- **Lambda** -  Is a serverless computing platform that allows you to run code without having to provision or manage infrastructure. A user can simply create the code and upload it as a .zip file or container image. It can handle code execution requests at any scale, from a few dozen per day to hundreds of thousands per second. It saves money because you only pay for the compute time you utilize (per millisecond).
		![[zIMG-AWS-ICON-Lambda.svg]]
- **Outpost** - Allow you to execute some AWS services locally while also connecting to a wide range of services accessible in the local AWS Region. It is designed to serve workloads and devices that require low latency access to on-premises systems and local data processing.
		![[zIMG-AWS-ICON-Outpost.svg]]
- **Elastic** [Kubernetes](https://kubernetes.io/docs/concepts/overview/) Service (EKS) - K8s with EC2s of [Fargate](https://aws.amazon.com/fargate/). Facilitates running Kubernetes in the AWS environment. It assists you in managing Kubernetes clusters and applications in hybrid environments, as well as running Kubernetes in your data centers.
		![[zIMG-AWS-ICON-EKS.svg]]
- **Elastic Container Registry** (ECR) - It is the docker hub for all of your Amazon containers. It is a high-performance hosting service that may be used to store, share, and deploy a user’s container software. It is also used to securely share and download images over the HTTPS protocol, which includes automatic encryption and access controls.
		![[zIMG-AWS-ICON-ECR.svg]]
- **Fargate** - Is a container-specific serverless compute service. It operates on the pay-as-you-go principle which helps to optimize the cloud spending cost. It allows you to concentrate on developing applications rather than managing servers. It can also be used to run Amazon ECS and EKS tasks and services.
		![[zIMG-AWS-ICON-Fargate.svg]]
- **AppRunner** - Is mostly used by developers to deliver containerized web applications. It is a simple service that does not require any prior infrastructure construction skills. In a short period of time, even a novice can create and run secure web-scale applications. It provides high flexibility to automatically scale up and scale down the resources according to traffic.
		![[zIMG-AWS-ICON-AppRunner.svg]]

## Batch processing and Workflows (Hydroponic, Food processing)

- **Batch** - It is primarily a resource for developers, scientists, and engineers. It can conduct hundreds of thousands of batch computing jobs on AWS quickly and easily. It takes away the need for them to install and manage batch computing tools or server clusters, allowing them to concentrate on analyzing data and addressing problems.
		![[zIMG-AWS-ICON-Batch.svg]]
- **Step Functions** - multi-step workflows

# Database as a Service ([DBaaS](https://www.techtarget.com/searchdatamanagement/definition/database-as-a-service-DBaaS)) (Middle of the starship)

## Databases (Ship's computer core)

- **Database Migration Service** - Syncs On-Prem & AWS Databases
- **Relational Database Service** (RDS) - Managed EC2s [Postgres](https://cloud.google.com/learn/postgresql-vs-sql), MSSQL, ... Also known as Relational Database Service this Aws icon represents a bundle of managed services that simplify the setup, operation, and scaling of cloud databases. It can also be used to build web and mobile applications.
		![[zIMG-AWS-ICON-RDS.svg]]
- **Aurora** - Includes built-in security, high performance, serverless computation, up to 15 read replicas, automated multi-Region replication, a platform for globally distributed application deployment, and connections with other AWS services.
		![[zIMG-AWS-ICON-Aurora.svg]]
- **DynamoDB** - DynamoDB is a NoSQL (Key-Value) database service with millisecond response times. It enables the development of software applications, the creation of media metadata warehouses, the delivery of seamless shopping experiences, and the scaling of gaming platforms of any size.
		![[zIMG-AWS-ICON-DynamoDB.svg]]
- **DocumentDB** - [Managed MongoDB](https://www.knowledgenile.com/blogs/pros-and-cons-of-mongodb)
- **ElastiCache** - [Redis](https://backendless.com/redis-what-it-is-what-it-does-and-why-you-should-care/#:~:text=Redis%20is%20an%20open%20source,transactions%20and%20publish%2Fsubscribe%20messaging.) (Scratchpad) Is a fully-managed, in-memory data store service hat makes it easy to deploy, operate, and scale an in-memory cache in the cloud. It supports two popular open-source in-memory caching engines: Memcached and Redis.
		![[zIMG-AWS-ICON-Elasticache.png]]
- **Neptune** - Is a cloud-based, fully-managed graph database service. It is a purpose-built graph database that is optimized for storing and querying highly connected data, such as social networks, recommendation engines, and knowledge graphs.
		![[zIMG-AWS-ICON-Neptune.svg]]
- **Glue** - Is a fully-managed extract, transform, and load (ETL) service. It is designed to make it easy to move data between different data stores, such as S3, RDS, Redshift, and other databases, as well as transform and cleanse the data as needed.
		![[zIMG-AWS-ICON-Glue.svg]]
- **Glue DataBrew** - Is a fully managed data preparation service offered by Amazon Web Services (AWS). It’s designed to help users clean and transform data for analytics and machine learning tasks. AWS Glue DataBrew simplifies the process of data preparation by providing a visual interface that allows users to discover, clean, and transform data without the need for coding or complex ETL (Extract, Transform, Load) processes.
		![[zIMG-AWS-ICON-GlueDataBrew.svg]]
- **Glue Elastic Views** - Introduces a convenient feature for effortlessly constructing materialized views by amalgamating and duplicating data from various data repositories, all without the need for manual coding. Leveraging the power of AWS Glue Elastic Views, you can efficiently forge a virtual table, serving as a materialized view, by employing familiar Structured Query Language (SQL) with data originating from diverse source data stores.
		![[zIMG-AWS-ICON-GlueElasticViews.svg]]

## Big Data (Ship's library)

- **Redshift** - [Postgres](https://cloud.google.com/learn/postgresql-vs-sql) Data Warehouse. Is a fully managed, petabyte-scale data warehouse that allows users to store and analyze large amounts of data using SQL queries. AWS Redshift uses a columnar storage architecture, which makes it optimized for querying and analyzing large datasets.
		![[zIMG-AWS-Icons-Redshift.png]]
- **EMR** - Run your own [Hadoop](https://hadoop.apache.org/)
- **Athena** - SQL queries on big S3 text files

## Messaging Queues (Shuttle bay)

- **Kinesis** - It allows users to collect, process, and analyze real-time streaming data, such as log files and data from IoT (Internet of Things) devices.
		![[zIMG-AWS-ICON-Kinesis.svg]]
- **Kinesis Data Analytics** - Is specifically focused on processing and analyzing data that is continuously generated and ingested in real-time, such as data from IoT devices, clickstreams, logs, and more.
		![[zIMG-AWS-ICON-KinesisDataAnalytics.svg]]
- **Kinesis Data Firehouse** - Is a fully managed service provided by Amazon Web Services (AWS) that allows you to reliably load streaming data into various AWS services and data stores for near real-time analytics and batch data processing. It simplifies the process of ingesting, transforming, and delivering real-time data streams and makes it easier to work with streaming data at scale.
	![[zIMG-AWS-ICON-KinesisFirehouse.svg]]
- **Kinesis Video Stream** - Is a service enables you to capture, process, and store video and audio streams for a wide range of applications, including security monitoring, machine learning, analytics, and more. It is part of the AWS Kinesis family of services, which are designed to handle real-time data streams.
		![[zIMG-AWS-ICON-KinesisVideoStreams.svg]]
- **Kinesis Data Streams** - It allows you to collect and process large amounts of data in real-time, making it suitable for various use cases such as log and event data processing, real-time analytics, monitoring, and more. Kinesis Data Streams is often used in applications where data needs to be ingested, processed, and analyzed as it arrives.
		![[zIMG-AWS-ICON-KinesisDataStreams.svg]]
- **Simple Queue Service** (SQS) - Workers process tasks
- **Simple Notification Service** (SNS) - Simple notification service is a fully managed AWS pub/sub messaging, SMS, email, and mobile push notification service capable of fan-out messages to a large number of subscriber systems. [Webhook](https://www.redhat.com/en/topics/automation/what-is-a-webhook)
		![[zIMG-AWS-ICON-SNS.svg]]

## Infrastructure as a Service ([IaaS](https://www.techtarget.com/searchcloudcomputing/definition/Infrastructure-as-a-Service-IaaS)) (Lower decks of the starship)

## Compute (Engine room)

- **Elastic Compute Cloud** (EC2) - Is a virtual server which provides scalable computing infrastructure to run applications on the AWS platform
		![[zIMG-AWS-ICON-EC2.svg]]
- **EC2 Spot instance** - When an EC2 instance is not fully utilized, the leftover unused capacity is referred to as a spot instance. It is available for use at a lower cost than On-Demand.
		![[zIMG-AWS-ICON-SpotInstance.svg]]
- **Auto Scaling** - Is used to ensure application availability. It automatically adds and removes EC2 instances based on predetermined conditions.
		![[zIMG-AWS-ICON-AutoScaling.svg]]
- **Systems Manager** - Large EC2 fleet tools
- **Reserved Instances** - Best fixed EC2 savings
- **EC2 Saving Plan** - Up/Down in EC2 sizes
- **Compute Saving Plan** - Low savings, all compute

## Storage (Storage lockers)

- **Elastic Block Storage** (EBS) - Is a scalable service that provides high-performance block storage. It is also used to resize clusters for big data analytics engines as well as to deploy and scale databases like SAP HANA, Oracle, Microsoft SQL Server, MySQL, and others.
		![[zIMG-AWS-ICON-EBS.svg]]
- **Simple Storage Service** (S3) - A long-lasting object storage system designed to retrieve any quantity of data from anywhere. It can protect and store any amount of data from customers of any size.
		![[zIMG-AWS-ICON-S3.svg]]
	- **CloudFront** - Worldwide [CDN](https://www.cloudflare.com/learning/cdn/what-is-a-cdn/)
		![[zIMG-AWS-ICON-CloudFront.svg]]
	- **S3 Glacier** - These are intended to archive data and give the best possible performance. These have the most data retrieval flexibility and the lowest cost to store archived data on the cloud in 2 forms- for the long term and extremely long terms. All S3 Glacier storage types are infinitely scalable and extremely durable.
		![[zIMG-AWS-ICON-S3Glacier.svg]]
- **Elastic File Store** (EFS) - NAS storage
- **Snowball** - Is an excellent tool for moving databases, backups, archives, documents, or media material to the cloud, especially when network resources are challenged. This AWS icon can be used in an AWS architecture diagram to speed up the upload of terabytes of offline data or distant storage to the cloud. That too with no restrictions on storage or processing power.
		![[zIMG-AWS-ICON-Snowball.svg]]
- **FSX** - It allows to run, and scale feature-rich and high-performance workloads or file systems with only a few clicks. It can also be used to Migrate any workloads to the cloud and to Increase development and test agility as well.
		![[zIMG-AWS-ICON-FSX.svg]]
- **GP2** - Is the standard EBS, elastic block storage that is backed up by solid-state drives (SSDs). It is utilized for a variety of transactional workloads such as development and testing environments, low-latency interactive applications, and boot volumes.
		![[zIMG-AWS-ICON-GP2.jpg]]
- **GP3** - Is an improved version of GP2 that debuted in 2020. It allows users to provision performance without worrying about storage as it is independent of storage capacity. As well it is  20% less expensive than GP2.
		![[zIMG-AWS-ICON-GP3.jpg]]

## Networking (Lower decks of the starship)

- **Virtual Private Cloud** (VPC) - Is often known as a virtual private cloud; it defines and launches AWS resources in a logically isolated virtual network. It is used to construct a basic website or blog, as well as to create hybrid connections and host a multi-tier web application.
		![[zIMG-AWS-ICON-VPC.svg]]
- **Public Subnet** - Internet-facing servers
- **Private Subnet** - Isolated servers
- **Client VPN** - Is a client-based VPN solution that allows you to securely access AWS resources and on-premises network resources from anywhere. It is a simple-to-use AWS-managed service with high elasticity and secure communications.
		![[zIMG-AWS-ICON-ClientVPN.svg]]
- **Site-to-site VPN**  - Link data center to AWS
- **Transit Gateway** - A transit gateway facilitates your network by acting as a cloud router, allowing each new connection to be established only once. A single gateway can be used to connect Amazon VPCs, AWS accounts, and on-premises networks. It gives you more control and increased security.
		![[zIMG-AWS-ICON-TransitGateway.svg]]
- **Direct Connect** - Direct connect establishes a dedicated network link to AWS. It improves application performance by connecting directly to AWS and bypassing the public internet. It allows you to handle big databases, create hybrid networks, and expand your existing user network.
		![[zIMG-AWS-ICON-DirectConnect.svg]]
- **Global Accelerator** - Internet fast lane
- **NAT Gateway** - Inside door knob
- **Internet Gateway** - Complete door knob
- **Elastic IP** - static IP
- **Elastic Load Balancer** ([ELB](https://aws.amazon.com/what-is/load-balancing)) - Distributes the load of incoming traffic in one or more than one availability zones and to other resources such as EC2 instances, containers, and IP addresses. It also keeps a check on the health of instances and transfers the load only to healthy instances.
		![[zIMG-AWS-ICON-ELB.svg]]
	- **Application Load Balancer** (ALB) - Best for HTTP
	- **Network Load Balancer** (NLB) - TCP and streaming
- **Route53** - DNS (phone book)
		![[zIMG-AWS-ICON-Route53.svg]]

## Other

- **DataSync** - Datasync enables easy data migrations and expedites them securely to AWS while providing end-to-end security. Moreover, it reduces costly on-premises data moving and can easily manage data movement workloads as well.
		![[zIMG-AWS-ICON-DataSync.svg]]
- **Storage Gateway** - The AWS storage gateway gives on-premises apps access to almost limitless cloud storage. Its on-premise advantages include low-latency data access, the ability to retain user and application workflows, limitless cloud storage, and support for critical capabilities such as encryption, audit logging, and so on.
		![[zIMG-AWS-ICON-StorageGateway.svg]]
- **Elastic Disaster Recovery** - Is a highly scalable and cost-effective disaster recovery service. This AWS icon can quickly restore operations in the event of unanticipated occurrences such as software problems or data-center hardware outages.
		![[zIMG-AWS-ICON-DisasterRecovery.svg]]
- **Backup** - It is an AWS service that allows you to centrally manage and automate backups. It is a hybrid data protection service capable of backing up your application data stored in AWS Storage Gateway volumes.
		![[zIMG-AWS-ICON-Backup.svg]]
- **TimeStream** - Amazon Timestream is a serverless service with auto-scaling that provides high performance at a cheap cost. This allows for easier data access that is always encrypted.
		![[zIMG-AWS-ICON-TimeStream.svg]]
- **Private LInk** - Private links allow private connectivity between VPCs and services hosted on AWS or on-premises. It secures your traffic and makes network management easier and you can utilize all of this without revealing your information to the internet.
		![[zIMG-AWS-ICON-PrivateLink.svg]]



