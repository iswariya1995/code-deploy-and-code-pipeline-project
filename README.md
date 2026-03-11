AWS CI/CD Pipeline with CodeDeploy & CodePipeline










This project demonstrates a fully automated CI/CD pipeline on AWS using CodePipeline and CodeDeploy to deploy a web application to an EC2 instance whenever a new version of the application is uploaded to Amazon S3.

The goal of this project is to showcase DevOps automation using AWS native services.

📌 Project Overview

The pipeline performs the following tasks automatically:

1️⃣ Developer updates the application
2️⃣ Application package is uploaded to Amazon S3
3️⃣ AWS CodePipeline detects the change
4️⃣ Pipeline triggers AWS CodeDeploy
5️⃣ CodeDeploy deploys the application to EC2 Web Server
6️⃣ SNS sends deployment notifications

🏗 Architecture
Architecture Flow

Developer → S3 Bucket → CodePipeline → CodeDeploy → EC2 Web Server

🔁 CI/CD Pipeline Workflow
4
Pipeline Stages
Stage	AWS Service	Description
Source	Amazon S3	Stores application package
Build	Skipped	Not required for this project
Deploy	AWS CodeDeploy	Deploys application to EC2
📂 Project Structure
aws-cicd-codedeploy-pipeline/
│
├── README.md
│
├── sampleapp/
│   ├── index.html
│   ├── appspec.yml
│   │
│   └── scripts/
│       ├── httpd_install.sh
│       ├── httpd_start.sh
│       └── httpd_stop.sh
│
└── screenshots/
    ├── codepipeline-success.png
    ├── codedeploy-success.png
    └── website-output.png
🌐 Sample Application

index.html

<html>
<h2> Sample App Version 1 </h2>
</html>

The application will be deployed to:

/var/www/html/index.html

on the EC2 instance.

⚙️ appspec.yml

This file tells AWS CodeDeploy how to deploy the application.

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
#!/bin/bash
yum install -y httpd
Start Apache
#!/bin/bash
systemctl start httpd
systemctl enable httpd
Stop Apache
#!/bin/bash
systemctl stop httpd
systemctl disable httpd
🛠 Infrastructure Setup
EC2 Instances
Instance	Purpose
Developer Machine	Build and upload application
Web Server	Deployment target

Instance type:

t2.micro
☁ Upload Deployment Package

Create zip package:

zip -r ../sampleapp.zip .

Upload to S3:

aws s3 cp sampleapp.zip s3://your-bucket-name
🚀 Deployment

When a new version of sampleapp.zip is uploaded to S3:

1️⃣ CodePipeline detects change
2️⃣ Pipeline starts automatically
3️⃣ CodeDeploy deploys new version to EC2

🔔 Notifications

Deployment notifications are sent using Amazon SNS.

Supported alerts:

Deployment started

Deployment succeeded

Deployment failed

Notification channels:

Email

SMS

📸 Screenshots
CodePipeline Execution
screenshots/codepipeline-success.png
CodeDeploy Deployment
screenshots/codedeploy-success.png
Web Application Output
screenshots/website-output.png
🌍 Application Output

Open browser:

http://<EC2-PUBLIC-IP>

Example output:

Sample App Version 2
🧹 Cleanup

To avoid AWS charges, delete:

EC2 Instances

CodeDeploy Application

CodePipeline

S3 Bucket

SNS Topic

IAM Roles

📚 Skills Demonstrated

This project demonstrates:

AWS DevOps

CI/CD Pipeline Design

Infrastructure Automation

Application Deployment Automation

Cloud Monitoring and Notifications
