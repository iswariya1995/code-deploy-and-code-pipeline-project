
AWS CI/CD Pipeline: Automated Web Deployment
📌 Project Overview
This project implements a robust Continuous Integration and Continuous Deployment (CI/CD) pipeline using AWS native services. It automates the lifecycle of a web application, from source code updates in Amazon S3 to automated deployment on Amazon EC2 instances using AWS CodeDeploy, all orchestrated by AWS CodePipeline.

🏗 Architecture
Source: Amazon S3 (Versioned)

Orchestration: AWS CodePipeline

Deployment: AWS CodeDeploy (In-place deployment)

Compute: Amazon EC2 (Amazon Linux 2)

Notifications: Amazon SNS (Email & SMS alerts)

📂 Project Structure
To ensure successful deployment, the repository is organized as follows:

Plaintext
.
├── index.html          # Main web application file
├── appspec.yml         # AWS CodeDeploy configuration file
├── scripts/            # Lifecycle event hooks
│   ├── httpd_install.sh
│   ├── httpd_start.sh
│   └── httpd_stop.sh
└── README.md           # Project documentation
🚀 Implementation Steps
1. Identity & Access Management (IAM)
EC2 Instance Role: Created with AmazonS3FullAccess to allow the CodeDeploy agent to pull artifacts.

CodeDeploy Service Role: Created with the AWSCodeDeployRole managed policy to allow AWS to manage EC2 deployments.

2. Infrastructure Setup
Web Server: Launched an EC2 (t2.micro) running Amazon Linux 2.

Agent Installation:

Bash
sudo yum update -y
sudo yum install ruby wget -y
wget https://aws-codedeploy-us-east-1.s3.amazonaws.com/latest/install
chmod +x install
sudo ./install auto
3. Application Configuration (AppSpec)
The appspec.yml file defines the deployment workflow. It maps the index.html to the Apache web directory (/var/www/html/) and triggers the bash scripts during specific lifecycle events:

BeforeInstall: Runs httpd_install.sh

AfterInstall: Starts the service via httpd_start.sh

ApplicationStop: Gracefully stops the service via httpd_stop.sh

4. Pipeline Orchestration
Source Stage: Configured S3 as the source provider. (Note: Bucket Versioning must be enabled).

Deploy Stage: Linked to the CodeDeploy application and deployment group.

Automation: Any update to the .zip file in the S3 bucket triggers the pipeline automatically.

5. Monitoring & Notifications
Integrated Amazon SNS to provide real-time updates.

Subscribers: Configured Email and SMS protocols to receive success/failure notifications.

🛠 Tech Stack
Cloud: AWS (EC2, S3, IAM, SNS)

CI/CD: CodePipeline, CodeDeploy

Scripting: Bash, YAML, HTML

Web Server: Apache (httpd)

🧹 Clean Up
To avoid unnecessary AWS charges, ensure the following are deleted:

CodePipeline & CodeDeploy configurations.

EC2 Instances.

S3 Buckets and SNS Topics.
