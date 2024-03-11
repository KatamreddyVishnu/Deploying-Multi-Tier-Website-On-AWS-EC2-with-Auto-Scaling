
# Deploying-Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling

Description: Amazon Elastic Compute Cloud (Amazon EC2) provides scalable computing capacity in the Amazon Web Services (AWS) cloud. Using Amazon EC2 eliminates your need to invest in hardware up front, so you can develop and deploy applications faster. You can use Amazon EC2 to launch as many or as few virtual servers as you need, configure security and networking, and manage storage. Amazon EC2 enables you to scale up or down to handle changes in requirements by using Auto-Scaling service in AWS.

# First let's start by understanding a few key terms :
# What is EC2?
EC2 stands for Elastic Compute Cloud, which is a web service provided by Amazon Web Services (AWS). EC2 is a central part of AWS and offers scalable computing capacity in the cloud. It allows users to rent virtual servers, known as instances, on a pay-as-you-go basis. These instances can be easily configured and scaled based on the user's computing requirements. EC2 instances come with a variety of instance types optimized for different use cases, such as compute-optimized, memory-optimized, storage-optimized, and more. Users have full control over their instances, including the ability to start, stop, terminate, and configure them. EC2 is widely used for hosting applications, websites, and for running various types of workloads in the cloud. It provides flexibility, scalability, and reliability for computing resources in the AWS cloud environment.

# What is a Multi-Tier Website?
Multi-tier website refers to a web architecture that is organized into different layers or tiers, each serving a specific function in the overall operation of the site. The three main tiers are the presentation layer (or user interface), the business logic layer, and the data layer. The presentation layer is what users see and interact with on the website. The business logic layer contains the application's logic and rules, handling tasks like processing user inputs and making decisions. The data layer manages the storage and retrieval of information from databases. This architectural approach helps in creating a scalable, modular, and maintainable web system, where changes or updates in one tier do not necessarily affect the others.

# What is RDS?
Amazon RDS (Relational Database Service) is a fully managed database service offered by Amazon Web Services (AWS), enabling users to easily set up, operate, and scale relational databases in the cloud. Supporting various database engines such as MySQL, PostgreSQL, Oracle, and Microsoft SQL Server, RDS automates routine administrative tasks like backups and patching, allowing users to focus on application development. With features like scalability, high availability through Multi-AZ deployments, security enhancements, and the option for read replicas, RDS provides a reliable and efficient solution for organizations seeking a cloud-based, hassle-free relational database management system.

# What is AutoScaling?
Auto Scaling in AWS is a service that automatically adjusts the number of compute resources (such as EC2 instances) in a group to match the desired capacity specified by the user. The primary goal of Auto Scaling is to ensure that you have the right amount of resources available to handle varying workloads, helping to maintain application availability and manage costs effectively.

# What is an Auto-Scaling Group?
Auto Scaling Groups (ASG) in AWS allow for the automatic adjustment of the number of EC2 instances based on application demand. By defining policies tied to metrics like CPU utilization or network traffic, ASGs dynamically scale instances up or down, ensuring optimal performance and resource utilization. These groups distribute instances across multiple Availability Zones for increased fault tolerance, and they seamlessly integrate with features like Elastic Load Balancing and AWS CloudWatch for efficient load distribution and monitoring. ASGs simplify the management of instances, offering a scalable and resilient solution that adapts to changing workloads in a cost-effective manner.

# Let's Start implementation
Prerequisite : AWS account
## Step 1: Initial Setup and Deployment of EC2 instance

- Name : Multi-Tier-Website
- Amazon Machine Image : Select Ubuntu Server 22.04 LTS
- Instance type : Select t2.micro
- Key Pair : Create new key Pair
- Network settings : Default
- Firewall (security groups) : All traffic enable
- Configure storage : Default
- Click on Launch instance





![Screenshot (851)](https://github.com/Vishnu15-dev/Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/124747520/b60de815-1918-41b3-b08c-01945941a220)

- Now select instance and click on Connect
![Screenshot (852) png – ScreenClip](https://github.com/Vishnu15-dev/Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/124747520/9b8cab62-7dee-46d9-9afb-c036cb172c7a)

- Now again click on Connect
![Screenshot (853)](https://github.com/Vishnu15-dev/Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/124747520/bda13210-0295-4c40-b813-fa1f2b19cc71)


## Installation

Install apache2 

```bash
  sudo apt-get update
  sudo apt-get install apache2 -y
  cd /var/www/html

```
   
![Screenshot (854)](https://github.com/Vishnu15-dev/Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/124747520/9e7ed9be-a31e-49c3-a720-43a76fec100d)

- Now copy public ip of EC2 and past in google.
- Now you can able to see Apache2 default page.
![Screenshot (855)](https://github.com/Vishnu15-dev/Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/124747520/b8a19cf7-d03a-45d1-b379-fa5527d5dbec)

- Now remove the index.html file and create index.php file and uplode the php code into the file.
- Save the nano file using Ctrl + S and Ctrl + X
```bash
  sudo rm -rf index.html
  sudo nano index.php
  cd
```
To connect RDS we will need to install MySQL client compatible with PHP. For that we’ll use the following command.This command will import the packages from the remote repository and then install the php5.6, mysql-client and mysqli compatible with php5.6
```bash
   sudo add-apt-repository -y ppa:ondrej/php 
   sudo apt install php5.6 mysql-client php5.6-mysqli -y
```
- Check once you are able to access the php application in browser with EC2 public ip
![Screenshot (863)](https://github.com/Vishnu15-dev/Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/124747520/efc45c8f-a373-425a-a595-3e16624b5009)
## Step 2 : Creat RDS MySql Database :
- Search as RDS
- Click on create Database
- Select Standard create
- Select MYSQL 
![Screenshot (856)](https://github.com/Vishnu15-dev/Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/124747520/3978e71a-bf24-4a05-8e60-a24954ec3dc9)

- Select Free Tier
![Screenshot (857)](https://github.com/Vishnu15-dev/Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/124747520/23561ae1-3508-4772-bb90-82aa48833d1f)

- Credentials Settings :
  - Master Username : intel
  - Master password : intel123

![Screenshot (858)](https://github.com/Vishnu15-dev/Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/124747520/ee40a2dd-f171-4261-9c5c-2a1da1324d85)

- Existing VPC security group : Select security group in which All Traffic is enabled for incommmig or other wise enabled it first.
  
![Screenshot (859)](https://github.com/Vishnu15-dev/Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/124747520/5731317a-f3ba-495c-9c50-3d63b7a752b1)

- Additional conifiguration : 
    - Initial database name : intel
    - 
![Screenshot (860)](https://github.com/Vishnu15-dev/Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/124747520/29f9c0a6-3241-4b43-a862-25408ab8bd62)

- Click on Create database and waite for few minutes to create .
  
![Screenshot (861)](https://github.com/Vishnu15-dev/Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/124747520/6bd77e68-a779-453e-803e-2bdb011ed6e8)

- Now copy the Endpoint of RDS 

![Screenshot (862)](https://github.com/Vishnu15-dev/Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/124747520/3b5a82ce-1884-4d1b-8c3a-b3ee66c79057)

- Again connect to EC2 instance 
```bash
  cd /var/www/html
  ls
  sudo nano index.php

  ```
- Replace the RDS Endpoint in index.php file at $servername : "database-1.ct8qusq0cw0c.us-east-1.rds.amazonaws.com"

![Screenshot (864)](https://github.com/Vishnu15-dev/Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/124747520/a8689747-d91e-42f7-9d24-7a6f200642b2)

- Run these commands
```bash
  sudo apt install mysql-server -y
  sudo mysql -h <mysql-endpoint> -u <username> -p

```
- click on Enter
- Type password
- Now you are able to connect to MYSQL server
```bash
  show databases;
```
- you can able to see "intel" database is created.

![Screenshot (866)](https://github.com/Vishnu15-dev/Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/124747520/1a3f11bd-a3a7-4543-aa8f-d8c0944840a4)

- Use following SQL commands
```bash
  show databases;
  use intel;
  create table data(firstname varchar(30), email varchar(30));
  insert into data values("aws","aws@gmail.com");
  show tables;
  select * from data;

```

![Screenshot (867)](https://github.com/Vishnu15-dev/Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/124747520/4a89d739-9085-4f16-8351-fade73049e42)

- Now we can able to uplode date into databases through EC2 public ip
  
![Screenshot (868)](https://github.com/Vishnu15-dev/Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/124747520/141d28e7-e618-460b-a525-b3ed8926f039)

- Finally we re-check the database if the Name and Email values has been updated or not.
  
```bash
  select * from data;
```

![Screenshot (869)](https://github.com/Vishnu15-dev/Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/124747520/7ff59db1-bea8-4220-8d80-63800c493fb2)

## Step 3 : Creating Auto-Scaling Group for High Availability of Website

### 1) Create Launch Templates 
- First select the instance 
- Click on Actions --> Images and Templates --> Creat template from instance.
- Only give Launch Template name and click on Create Launch template.

![Screenshot (870)](https://github.com/Vishnu15-dev/Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/124747520/2efe0efc-a1ce-4979-8e13-89cad2ee451d)

### 2) Creat Target Group
- Only give Target group name
- Click on Next

![Screenshot (872)](https://github.com/Vishnu15-dev/Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/124747520/cb7f569c-347c-458c-92d2-c2417df6f884)

- Select the instance which are under Register targets
- Click on "Include as pending below"

![Screenshot (873)](https://github.com/Vishnu15-dev/Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/124747520/49cc5bee-ed1e-4795-93ae-190d4e53895b)

- Click on "Create target group"

![Screenshot (874)](https://github.com/Vishnu15-dev/Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/124747520/40bed12e-b208-42b7-a020-42f8aa256304)

### 3) Create Application Load Balancer
- Select Application Load Balancer

![Screenshot (875)](https://github.com/Vishnu15-dev/Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/124747520/0f197dfe-9a5e-407a-a2af-1a716e304303)

- Give Load Balancer Name in Basic configuration

![Screenshot (876)](https://github.com/Vishnu15-dev/Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/124747520/bcae07d3-e239-47c3-afd6-87401b75e134)

- Select at least 2 availability zones where the Website has to run

![Screenshot (877)](https://github.com/Vishnu15-dev/Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/124747520/d061f327-62dd-4fa5-aef3-b494a44cce8d)

- Selet the Target Group

![Screenshot (878)](https://github.com/Vishnu15-dev/Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/124747520/1a23352d-cc1c-4821-ab37-63a366c1066f)

- Now Load Balancer is created
- Copy the DNS name : "Multi-Tier-LB-2039917078.us-east-1.elb.amazonaws.com" re-check you are able to access the Website.

![Screenshot (879)](https://github.com/Vishnu15-dev/Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/124747520/ed236d82-adab-4870-b8d0-8fb512e7731b)

### 4) Create Auto-Scaling Group
- For creating Auto-Scaling Group give name for it 
- Select the Templates which was created in previously step.
- 
![Screenshot (880)](https://github.com/Vishnu15-dev/Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/124747520/c40d92c5-a3fb-4733-ab80-0d8a632cb9f4)

- Selet atleast 2 AZ 

![Screenshot (881)](https://github.com/Vishnu15-dev/Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/124747520/1df52917-a547-4b2f-9fff-ae6e1660d9d3)

- Click on "Attach to an existing load balancer"

![Screenshot (882)](https://github.com/Vishnu15-dev/Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/124747520/7c05d8d4-0310-45a8-b8cb-be821f2ba0f0)

- Group Size: 
  - Desired Capacity : 2
- Scaling :
  - Min Desired Capacity : 1
  - Max Desired Capacity : 5
![Screenshot (883)](https://github.com/Vishnu15-dev/Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/124747520/82accc7d-d5dc-41fd-b1df-f40b22a079b0)

- Select Target tracking scaling policy
- Metric Type : CPU Utilization
- Target value : 80


![Screenshot (884)](https://github.com/Vishnu15-dev/Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/124747520/8c25f359-e842-456d-b233-ddf1fc0d3888)

- Leave other settings as Default and click on Next
- Finally click on create Auto-Scaling group

![Screenshot (885)](https://github.com/Vishnu15-dev/Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/124747520/ba2c9914-69e6-49e8-a7f1-8549d21fee3b)

- Finally we have added instances to Targer group

![Screenshot (886)](https://github.com/Vishnu15-dev/Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/124747520/bf543c73-b1b5-4b90-b684-37b5d2371052)

- Once again check database is actively storing data from Website
![Screenshot (887)](https://github.com/Vishnu15-dev/Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/124747520/10e8c100-de2c-479b-9df6-53ea62de9680)

- Finally we Done it.

![Screenshot (888)](https://github.com/Vishnu15-dev/Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/124747520/8bcb542d-ecea-460f-ab7e-9717a0619261)
- At last Delete all resources Which was created by you.
