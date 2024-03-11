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






![Screenshot (851)](https://github.com/KatamreddyVishnu/Deploying-Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/155063589/104f2271-6e0c-42d4-9b0a-3cc4a1b8978f)

- Now select instance and click on Connect

![Screenshot (852)](https://github.com/KatamreddyVishnu/Deploying-Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/155063589/3e0f6557-74d3-41d0-be64-5e6f8acd93da)

- Now again click on Connect
![Screenshot (853)](https://github.com/KatamreddyVishnu/Deploying-Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/155063589/6d277bd0-38dd-40bb-b08b-08c9464ccb5d)

## Installation

Install apache2 

```bash
  sudo apt-get update
  sudo apt-get install apache2 -y
  cd /var/www/html

```
   
![Screenshot (854)](https://github.com/KatamreddyVishnu/Deploying-Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/155063589/917670b1-a07d-4ae3-9040-bd2900e6108d)


- Now copy public ip of EC2 and past in google.
- Now you can able to see Apache2 default page.

![Screenshot (855)](https://github.com/KatamreddyVishnu/Deploying-Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/155063589/92bf116b-7b9c-4240-9bc6-b5955512b6da)

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
![Screenshot (863)](https://github.com/KatamreddyVishnu/Deploying-Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/155063589/7796fb2f-2538-49f0-9b12-114e2a40f64a)

## Step 2 : Creat RDS MySql Database :
- Search as RDS
- Click on create Database
- Select Standard create
- Select MYSQL 

![Screenshot (856)](https://github.com/KatamreddyVishnu/Deploying-Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/155063589/f91f40ef-8fae-46b8-9da1-bda25b014b65)

- Select Free Tier

![Screenshot (857)](https://github.com/KatamreddyVishnu/Deploying-Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/155063589/e2b9310b-3f85-4b8d-a511-47c4654099e3)

- Credentials Settings :
  - Master Username : intel
  - Master password : intel123


![Screenshot (858)](https://github.com/KatamreddyVishnu/Deploying-Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/155063589/30f1be6c-7e34-48e4-995f-9d1a1cd5ca62)

- Existing VPC security group : Select security group in which All Traffic is enabled for incommmig or other wise enabled it first.
  

![Screenshot (859)](https://github.com/KatamreddyVishnu/Deploying-Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/155063589/522a8d16-d24c-4a81-ad96-349f0ff581f1)

- Additional conifiguration : 
    - Initial database name : intel
      
![Screenshot (860)](https://github.com/KatamreddyVishnu/Deploying-Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/155063589/fcf6089f-4e52-4f21-9ba0-ed62b5701118)


- Click on Create database and waite for few minutes to create .
  
![Screenshot (861)](https://github.com/KatamreddyVishnu/Deploying-Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/155063589/86913dc4-94da-4b34-a13a-130ee5c20ce4)

- Now copy the Endpoint of RDS 

![Screenshot (862)](https://github.com/KatamreddyVishnu/Deploying-Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/155063589/b49f2fdf-e5c4-4456-8918-5be45e40cd01)

- Again connect to EC2 instance 
```bash
  cd /var/www/html
  ls
  sudo nano index.php

  ```
- Replace the RDS Endpoint in index.php file at $servername : "database-1.ct8qusq0cw0c.us-east-1.rds.amazonaws.com"

![Screenshot (864)](https://github.com/KatamreddyVishnu/Deploying-Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/155063589/c0881a80-33e2-4857-8b26-bc9608bb3df6)

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


![Screenshot (866)](https://github.com/KatamreddyVishnu/Deploying-Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/155063589/618a95d3-b71c-40af-aedd-ebe776f1abd6)

- Use following SQL commands
```bash
  show databases;
  use intel;
  create table data(firstname varchar(30), email varchar(30));
  insert into data values("aws","aws@gmail.com");
  show tables;
  select * from data;

```
![Screenshot (867)](https://github.com/KatamreddyVishnu/Deploying-Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/155063589/1de97b3f-98ec-4a00-968b-da6012b068a0)


- Now we can able to uplode date into databases through EC2 public ip
  

![Screenshot (868)](https://github.com/KatamreddyVishnu/Deploying-Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/155063589/85b45278-fcf1-4da3-b546-06d5108d981c)

- Finally we re-check the database if the Name and Email values has been updated or not.
  
```bash
  select * from data;
```


![Screenshot (869)](https://github.com/KatamreddyVishnu/Deploying-Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/155063589/92b7dd44-536f-4af0-8e98-8c2713284a2c)

## Step 3 : Creating Auto-Scaling Group for High Availability of Website

### 1) Create Launch Templates 
- First select the instance 
- Click on Actions --> Images and Templates --> Creat template from instance.
- Only give Launch Template name and click on Create Launch template.


![Screenshot (870)](https://github.com/KatamreddyVishnu/Deploying-Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/155063589/284f3553-6848-4c60-8f38-506badef5401)

### 2) Creat Target Group
- Only give Target group name
- Click on Next


![Screenshot (872)](https://github.com/KatamreddyVishnu/Deploying-Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/155063589/5aae5425-0cd3-441f-8fb8-e36609345983)

- Select the instance which are under Register targets
- Click on "Include as pending below"

![Screenshot (873)](https://github.com/KatamreddyVishnu/Deploying-Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/155063589/53154bc0-f133-416c-ba5c-1efaab207ce4)


- Click on "Create target group"

![Screenshot (874)](https://github.com/KatamreddyVishnu/Deploying-Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/155063589/b19cfa20-e5d0-433c-8ea6-6a8f0fe2190b)

### 3) Create Application Load Balancer
- Select Application Load Balancer

![Screenshot (875)](https://github.com/KatamreddyVishnu/Deploying-Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/155063589/f4ed1e44-e5a7-4f35-aed4-f8746b735078)

- Give Load Balancer Name in Basic configuration


![Screenshot (876)](https://github.com/KatamreddyVishnu/Deploying-Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/155063589/6c61e897-c8f5-453d-a36f-87365ba81022)

- Select at least 2 availability zones where the Website has to run

![Screenshot (877)](https://github.com/KatamreddyVishnu/Deploying-Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/155063589/5eaafd4c-076c-47a7-a817-842c731b38b0)

- Selet the Target Group

![Screenshot (878)](https://github.com/KatamreddyVishnu/Deploying-Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/155063589/0bb87d21-f8d4-4f85-9232-1b1a2cf2786e)

- Now Load Balancer is created
- Copy the DNS name : "Multi-Tier-LB-2039917078.us-east-1.elb.amazonaws.com" re-check you are able to access the Website.

![Screenshot (879)](https://github.com/KatamreddyVishnu/Deploying-Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/155063589/e5f76b99-5504-466e-b3f7-37f201c79825)

### 4) Create Auto-Scaling Group
- For creating Auto-Scaling Group give name for it 
- Select the Templates which was created in previously step.
  
![Screenshot (880)](https://github.com/KatamreddyVishnu/Deploying-Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/155063589/9edb6dfa-95c9-4747-b42d-d85858022acd)

- Selet atleast 2 AZ 


![Screenshot (881)](https://github.com/KatamreddyVishnu/Deploying-Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/155063589/84f9c5c9-70a1-4453-9c36-e9c488904aba)

- Click on "Attach to an existing load balancer"


![Screenshot (882)](https://github.com/KatamreddyVishnu/Deploying-Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/155063589/a7694bc3-7c7a-41c5-b538-35edf5080464)

- Group Size: 
  - Desired Capacity : 2
- Scaling :
  - Min Desired Capacity : 1
  - Max Desired Capacity : 5
![Screenshot (883)](https://github.com/KatamreddyVishnu/Deploying-Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/155063589/44e399d6-9836-496d-9473-219b68324a6a)

- Select Target tracking scaling policy
- Metric Type : CPU Utilization
- Target value : 80


![Screenshot (884)](https://github.com/KatamreddyVishnu/Deploying-Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/155063589/6f6cb9dc-c12a-4ae9-9375-1bbde9695ef5)

- Leave other settings as Default and click on Next
- Finally click on create Auto-Scaling group

![Screenshot (885)](https://github.com/KatamreddyVishnu/Deploying-Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/155063589/94aac569-4d3f-4ea5-90dd-c2a7e4b3c71d)


- Finally we have added instances to Targer group

![Screenshot (886)](https://github.com/KatamreddyVishnu/Deploying-Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/155063589/178ce21e-c9da-4f6d-9fce-681b71f0e8ed)


- Once again check database is actively storing data from Website

![Screenshot (887)](https://github.com/KatamreddyVishnu/Deploying-Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/155063589/827642f5-a393-408b-b5d9-1fd4b9f848eb)


- Finally we Done it.

![Screenshot (888)](https://github.com/KatamreddyVishnu/Deploying-Multi-Tier-Website-On-AWS-EC2-with-Auto-Scaling/assets/155063589/1f803ab5-2ed0-4602-9a97-af1786e79d0e)

- At last Delete all resources Which was created by you.
