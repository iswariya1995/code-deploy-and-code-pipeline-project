🚀 AWS CI/CD Pipeline with CodeDeploy & CodePipeline

This project demonstrates how to implement a Complete CI/CD pipeline using AWS services including:

AWS CodeCommit / S3 (Source)

AWS CodeBuild (optional)

AWS CodeDeploy (Deployment)

AWS CodePipeline (Automation)

Amazon EC2 (Web server)

Amazon S3 (Artifact storage)

IAM (Access control)

Amazon SNS (Notifications)

CloudWatch (Monitoring)

The pipeline automatically deploys a web application to an EC2 instance whenever new code is uploaded to the repository (S3).

📌 Architecture Overview

Developer Machine → Upload Code → S3 Bucket → CodePipeline Trigger → CodeDeploy → EC2 Web Server

Services used:

EC2 – Web server hosting the application

S3 – Stores deployment artifacts

CodeDeploy – Deploys code to EC2

CodePipeline – Automates CI/CD workflow

IAM – Roles and permissions

SNS – Deployment notifications

CloudWatch – Monitoring and logs

📂 Project Structure
sampleapp/
│
├── index.html
├── appspec.yml
│
└── scripts/
    ├── httpd_install.sh
    ├── httpd_start.sh
    └── httpd_stop.sh
🖥 Sample Application

index.html

<html>
<h2> Sample App Version 1 </h2>
</html>
⚙️ appspec.yml Configuration

This file tells CodeDeploy how to deploy the application.

version: 0.0
os: linux

files:
  - source: /index.html
    destination: /var/www/html/

hooks:
  BeforeInstall:
    - location: scripts/httpd_install.sh
      timeout: 300
      runas: root

    - location: scripts/httpd_start.sh
      timeout: 300
      runas: root

  ApplicationStop:
    - location: scripts/httpd_stop.sh
      timeout: 300
      runas: root
📜 Deployment Scripts
Install Apache

scripts/httpd_install.sh

#!/bin/bash
yum install -y httpd
Start Apache

scripts/httpd_start.sh

#!/bin/bash
systemctl start httpd
systemctl enable httpd
Stop Apache

scripts/httpd_stop.sh

#!/bin/bash
systemctl stop httpd
systemctl disable httpd
🛠 Setup Instructions
1️⃣ Create IAM Roles

Create two roles:

EC2 Role

Attach policy:

AmazonS3FullAccess

Name:

s3-ec2-full
CodeDeploy Role

Service:

CodeDeploy

Use case:

CodeDeploy
👤 Create IAM User (Developer)

Go to IAM → Users

Click Add User

Enable Programmatic Access

Download the .csv credentials

🖥 Create EC2 Instances

Create two Amazon Linux 2 EC2 instances:

Instance	Purpose
Developer Machine	Build & upload code
Web Server	Deployment target

Instance type:

t2.micro

Attach role:

s3-ec2-full
⚙ Install CodeDeploy Agent on Web Server

SSH into the web server and run:

sudo yum update -y
sudo yum install ruby -y
sudo yum install wget -y

wget https://aws-codedeploy-us-east-1.s3.amazonaws.com/latest/install
chmod +x install
sudo ./install auto

sudo service codedeploy-agent status
📦 Create Deployment Package

On the developer machine:

mkdir deploy_dir
cd deploy_dir

mkdir sampleapp
cd sampleapp

Create files:

index.html

appspec.yml

scripts/

Then zip the application:

zip -r ../sampleapp.zip .
☁ Upload Code to S3

Create a bucket:

aws24092021

Upload package:

aws s3 cp sampleapp.zip s3://aws24092021
🚀 Create CodeDeploy Application
aws deploy create-application --application-name sampleapp

Verify in the AWS Console.

⚙ Create Deployment Group

Settings:

Parameter	Value
Deployment Group	mygrp
Deployment Type	In-place
Environment	EC2 Instances
Tag Key	AppName
Tag Value	SampleApp

Disable Load Balancer.

🚀 Create Deployment

Go to CodeDeploy Console

Click Create Deployment

Select:

Revision Type: Amazon S3

Choose:

sampleapp.zip

Deployment will start automatically.

🔁 Create CodePipeline (CI/CD)

Pipeline stages:

Stage	Service
Source	S3
Build	Skip
Deploy	CodeDeploy

⚠ Important:

Enable S3 Bucket Versioning.

Otherwise the pipeline will fail.

🔄 Updating the Application

Modify index.html:

<html>
<h2> Sample App Version 2 </h2>
</html>

Recreate the zip:

zip -r ../sampleapp.zip .

Upload again:

aws s3 cp sampleapp.zip s3://aws24092021

CodePipeline will automatically trigger deployment.

🔔 Enable Notifications (SNS)

Create SNS topic:

mytopic

Add subscriptions:

Email

Protocol:

Email
SMS

Protocol:

SMS

Notifications will trigger for:

Deployment started

Deployment succeeded

Deployment failed

🌐 Verify Deployment

Open the EC2 public IP in a browser:

http://<EC2-PUBLIC-IP>

Expected output:

Sample App Version 2
🧹 Cleanup (Resource Deletion)

To avoid AWS charges, delete resources:

Terminate EC2 instances

Delete CodeDeploy application

Delete CodePipeline

Delete S3 bucket

Delete SNS topics

Delete IAM roles and users

📊 Key Learning Outcomes

Implement CI/CD using AWS services

Automate deployments with CodePipeline

Deploy applications using CodeDeploy

Manage infrastructure permissions with IAM

Use SNS for deployment notifications
