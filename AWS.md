# Cloud Computing Services 

IaaS - Linux / Windows - EC2 
PaaS - Linux / Windows + Apache + MySQL + PHP - Elastic 
SaaS - Linux / Windows + Apache + MySQL + PHP + Software/ Apps - Chime

Before IaaS come on premise which means everything is managed by the organization. Local cloud for an organization

Cloud Computing Deployment Models :-
- Public (Cloud)
- Private (On-Premises) 
- Hybrid (Public Cloud + Private Cloud)


EC2 - Elastic Compute Cloud 
EC2 allows users to rent virtual machines with varying configurations of CPU, memory, storage, and networking capacity, enabling them to perform computations,
run applications, and handle data processing tasks without the need to invest in physical hardware.

Compute refers to the resources and processing power provided to the EC2 servers

## So how do you launch a VM on PC 

- You have a laptop (Hardware)
- OS installed
- VM software (VMware)
- VM1 instance

- Launcing an instance on EC2
- AWS hardware
- PV/HVM
- Instance 1 or 2

VPC - Virtual Private Cloud - Used to create a separate isolated network 

# Connecting to the EC2 instance

- We can connect using Putty or Mobaxtrem
- Launcing an Apache web server on EC2
- sudo yum install httpd

  **Here the sudo vs su debate, sudo is super user do and su is used to switch the user       ......
  yum is the package manager which can be RedHat or anything (Yellowdog Updater, Modified), basically a package manager for the unix like service             .......
 httpd is the Apache HTTP Server, which is a widely used web server software. Web server software means that we can install and work on softwares on the web on this server, its an online pc for web softwares**

  - systemctl, systemctl is a versatile and essential tool for managing services and system states in Linux distributions
  - sudo systemctl start <service_name>
  - sudo systemctl start httpd ( The command activates the Apache HTTP Server service, allowing it to begin handling incoming HTTP requests. )
  - systemctl enable httpd
  - Incoming HTTP requests mean that you can access via web page using the address
  - Windows client can use putty to connect and linux client can use ssh
  - sudo -s (to start a new shell session with root privileges)
  - dnf -y install httpd PHP (dnf is successor of yum -y is for yes, this is for installing php)
  - cat >index.html (index.html is the file using this we can edit the file and then using cat we can view the file)
 
- ssh -i /path/to/private_key.pem user@hostname (This is the path to the private key file you want to use for authentication. It is typically a file with a .pem or .key extension)


  ## Storage
  - Direct Attach Storage (DAS): like pendrive, USB, Hard Diskm instance storage(EBS) - for cloud
  - Network Attached Storage (NAS): Can be accessed by all the users in a local network, also called EFS(Elastic File System)-for cloud . One PC becomes the master or server and shares it to nearby PC's. another example is a printer
  - Storage Area Network (SAN): EBS(Elastic Block Store)-for cloud, data is stored in data blocks and not file system. Data blocks means C:, D: etc these storage blocks
  - Simple Storage Service (S3): to store the objects, data can be stored on a URL
 
- fdisk -l (list all the disks available)
- mount /dev/xvdf1  /ebs (ebs is being mounted in the linux instance)
- df -h (report file system disk space usage df -kh will give the result in kB)
- So basically the hard disk can be included in the cloud VM using the AWS account and SAN, then this hard disk is being mounted above
- umount /ebs

**AMI**

So now let's say we create a VM, AMI (Amazon Machine Image). This VM need storage/hard drive. 
- If we launch an EC2 instance for compute, we can either use EBS or Instance Store
- Instance Store is temporary, once we terminate the EC2, data is gone
- Let's say we create an EC2 in AZ1 aread using EBS, then we can take EBS snapshots
- These snapshots take full backup on the first time and then it is incremental, these backups are stored in S3
- Now using this S3 we can create a new VM in any other region
- WE can also transfer files between 2 instances using EFS


**Launching an Instance**

- Name
- AMI
- Instance Type
- Key pair
- Network Settings
- Configure storage


**S3**

- We can store objects in S3 using a link so we don't need to initiate an instance
- create a bucket in some region
- We can make the bucket and objects public as well
- For this we need to follow the bucket policy

- Bucket Versioning - to revert to the older version

- Launch static website using S3
- There should be an HTML file in the bucket, then there is an option to host the website, whatever the html file holds become the website's script
- Amazon Athena can be used to do simple query SQL on S3
- Amazon Redshift Spectrum does more complex SQL queries on large datasets

**RDS**
- Relational Database Service
- Six database engine available are Amazon Aurora, MariaDB, MySQL, PostgreSQL, Oracle and SQL Server
- AWS Database Migration Service (DMS) can be used to migrate existing databases to Amazon RDS.

**DynamoDB**
- Here we use partition key and storage key
- it is automated on the aws website, just like interana we used to use
- We can enter the attributes and the values of partition and storage key to retrieve the data

Multiple NoSQL databases are supported :-
Key Value - Pairs of key and it's value
Document - Amazon DocumentDB, stored like in JSON {}
Graph - Amazon Neptune
In Memory - Amazon DynamoDB
Amazon Elasticsearch Service


**VPC**
- Virtual Private Cloud
- It has different ranges of IP address some start from 192, some from 172, some from 10
- so within a VPC we have something called a subnet, which is a range of IP addresses
- within a subnet we can create an instance, so like VPC will be for a region and we create our private/public subnet which has only our instance
- Then we have public/private routing table and vpc will also have a main route table
- VPC can be created using AWS console and then subnets within it
- echo "Apache Web Server launched in Public Subnet">index.html (this texts get stored in the file and displays the output also)
- Route Table is the set of rules where the network traffic is directed.
- resources in the VPC(EC2) and the internet, this connection is established using the Internet gateway
- Just like a city can have different neighborhoods with different rules (e.g., traffic flow, security), a VPC can have subnets with different access control rules
  (public or private).
- Each neighborhood (subnet) can have its own road system (IP addressing), and the roads are controlled by traffic signs (network ACLs and route tables).

# Custom VPC setup 

- Setup VPC lab
- create custom vpc (ourvpc)
- create 2 public and 2 private subnets
- create internet gateway (ourig) and attach to VPC
- create and config routing table (public routing table)
- create webserver security group and add rules (webserver)
- Launch internet facing linux apache web server instance (webhosting)
- create database security group and add rules (database)
- private subnets for database (db-subnet)
- launch backend RDS instance (ourdb)
- connect to webserver instance using Putty
- deploy simple PhP/MySQL application in custom VPC 

So website can be deployed on a public subnet which can be accessed to the internet, and the database for that website cannot be accessed through the internet and 
is deployed on a private subnet. So we use gateway which allows the public subnet to connect to the internet. 

Next is to configure the routing tables, they control the network flow in a VPC. These tables define where the data needs to go. The table has the destination address and gateways through which the data will flow mentioned. 

Allows SSH (22), HTTP (80), and HTTPS (443). These are all security groups that are created in the next steps. Ensures who can access what in the VPC. Only necessary ports are opened, reducing the risk of unauthorized access.

A webpage is hosted on a server - in this case, it is the Apache Web Server.
PHP script is deployed to interact with the database, fetch data, and display it on the website. The website is coded in PHP.

All the installing part of the apache web server and php to process web requests can be done on EC2 instance. 

Web Server (EC2 + Apache + PHP) → In Public Subnet
Database (Amazon RDS - MySQL) → In Private Subnet (inside a Database Subnet Group)

**ELB**

- Elastic Load Balancer
- Basically balances the incoming app or network traffic, ensuring no instance is overloaded
- Application Load Balancer (ALB), Network Load Balancer (NLB)

-  Imagine you have a website with a /products page and a /blog page. The ALB can route traffic to different EC2 instances based on the URL path:
/products -> EC2 instance running your product service.
/blog -> EC2 instance running your blog service.

-  If you need to load balance at a very low level, like for game servers or VoIP services, the NLB will route requests to EC2 instances that are optimized for handling millions of requests per second, quickly and efficiently.

-  Auto Scaling Group (ASG)
When traffic increases, ASG will automatically launch new EC2 instances to meet the demand (more "houses" are built in the neighborhood).
When traffic decreases, ASG will terminate unnecessary EC2 instances to save costs (some "houses" are demolished).


**CloudWatch**
- powerful monitoring and observability tool in AWS
- CloudWatch collects metric data from various AWS services, such as EC2, RDS, Lambda, etc. Metrics might include things like:

CPU utilization of EC2 instances
Network traffic of load balancers
Disk I/O of EBS volumes
Memory usage (for EC2 instances with custom CloudWatch agents)

- CloudWatch can aggregate and store logs from AWS services like Lambda, API Gateway, or custom log files from your EC2 instances.
 This is helpful for tracking application activity, errors, or system events.

- CloudWatch can be integrated into your CI/CD pipeline, serverless architecture, or any AWS-based application to provide full observability.
- CloudWatch is the go-to tool for monitoring AWS environments, Prometheus, and New Relic are excellent choices for multi-cloud or hybrid environments,
  Splunk and the ELK stack excel at log aggregation, analysis, and real-time security monitoring.

- Simple Notification Service (SNS) - This sends the notification to email


**Stateful vs Stateless Firewall in AWS**
- Both are concepts of network security ensuring the data that flows out of the VPC

  Stateful Firewall
  - You have an EC2 instance running a web application (e.g., a website).
You set an inbound rule to allow HTTP traffic (port 80) from anywhere (0.0.0.0/0).
If someone makes an HTTP request to your website (port 80), the security group will allow the incoming traffic.
The web server sends the response back to the requester (HTTP response), and the security group automatically allows the outbound response traffic,
 even if you haven't set a rule for outbound traffic.

- A stateless firewall is like a security guard who checks every individual request without remembering any past requests. Each packet is treated as a completely new, isolated event.
You create an inbound rule to allow HTTP traffic (port 80) from anywhere (0.0.0.0/0).
You also need to create an outbound rule to allow responses (to send the HTTP response back to the client).
If you forget the outbound rule, the response from the server won’t be allowed out, even though the incoming request was allowed.

**Five IP addresses are reserved in each subnet.
For example in subnet“10.0.1.0/24”**

First Address 10.0.1.0 (Network Address)
Second Address 10.0.1.1 (VPC Router)
Third Address 10.0.1.2 (DNS Address)
Fourth Address 10.0.1.3 (Reserved)
Last Address 10.0.1.254 (Broadcast Address)

AWS WAF (Web Application Firewall) - It protects web applications from common web exploits.

AWS Shield (Standard and Advanced) - It protects applications against DDoS attacks.

A DDoS (Distributed Denial of Service) attack is a cyberattack where multiple compromised computers (botnets) flood a target system with traffic to overwhelm and crash it. The goal is to make a website, application, or network unavailable to legitimate users.

Amazon Inspector -  check the applications for vulnerabilities and deviations

Amazon GuardDuty - It offers intelligent threat detection for your AWS resources

Amazon Macie - discover and protect your sensitive data at scale.

AWS CloudTrail - It continuously log your account activity.

AWS Trusted Advisor - It inspects your AWS environment and gives real-time recommendations


**Docker**
- In earlier days, people used to work on physical machine, then came VMs and now it is containers everywhere.
- 
