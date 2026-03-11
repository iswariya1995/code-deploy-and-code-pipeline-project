AWS CI/CD Pipeline: Automated Web Deployment
This project demonstrates a complete Continuous Interaction/Continuous Deployment (CI/CD) pipeline using AWS native services. It automates the process of deploying a web application from a developer environment to a fleet of EC2 instances via S3, using AWS CodePipeline and CodeDeploy.

## Project Overview
The goal is to model a workflow where any code change pushed to an S3 bucket automatically triggers a deployment to a web server.

Core Services Used
AWS CodePipeline: Orchestrates the workflow.

AWS CodeDeploy: Automates the deployment to EC2.

Amazon S3: Acts as the artifact repository (Source).

Amazon EC2: Hosts the web server and the developer environment.

AWS IAM: Manages permissions for EC2 and CodeDeploy.

Amazon SNS: Provides email and SMS notifications on deployment status.

## Architecture & Setup
1. IAM Role Configuration
Before launching instances, specific roles must be created:

EC2-S3-CodeDeploy Role: Allows EC2 to fetch artifacts from S3.

CodeDeploy Service Role: Grants CodeDeploy permission to manage EC2 instances.

2. Environment Preparation
Developer Machine: An EC2 instance (Amazon Linux 2) where the code is written, zipped, and pushed to S3.

Web Server (Target): An EC2 instance with the CodeDeploy Agent installed.

Note: The CodeDeploy agent requires Ruby and Wget. Use the following to install:

Bash
sudo yum update -y
sudo yum install ruby wget -y
wget https://aws-codedeploy-us-east-1.s3.amazonaws.com/latest/install
chmod +x install
./install auto
## Application Structure
The application requires an appspec.yml file in the root directory to manage deployment lifecycle hooks.

YAML
version: 0.0
os: linux
files:
  - source: /index.html
    destination: /var/www/html/
hooks:
  BeforeInstall:
    - location: scripts/httpd_install.sh
  ApplicationStop:
    - location: scripts/httpd_stop.sh
  AfterInstall:
    - location: scripts/httpd_start.sh
## Deployment Steps
Step 1: Source Preparation
Create a simple index.html.

Create shell scripts in a /scripts folder to handle httpd installation and service management.

Zip the contents: zip -r sampleapp.zip .

Step 2: Push to S3
Push the revision to the S3 bucket using the AWS CLI:

Bash
aws deploy push --application-name sampleapp --s3-location s3://your-bucket-name/sampleapp.zip
Step 3: Configure CodePipeline
Source Stage: Select Amazon S3 and the specific bucket/key. Note: S3 Versioning must be enabled.

Deploy Stage: Select AWS CodeDeploy, choose your Application Name and Deployment Group.

## Notifications (SNS)
The project includes a monitoring layer using Amazon SNS:

Protocols: Email and SMS.

Triggers: Deployment Start, Success, and Failure.

Verification: Ensure you confirm the subscription via the automated email sent by AWS.

## Verification & Testing
Update index.html to "Version 2".

Re-zip and upload to S3.

Watch CodePipeline automatically detect the change and trigger CodeDeploy.

Access the EC2 Public IP to verify the updated content.

## Cleanup (Demolish)
To avoid unnecessary AWS charges, ensure you delete resources in this order:

Terminate EC2 Instances.

Delete CodePipeline and CodeDeploy applications.

Empty and delete S3 Buckets.

Delete SNS Topics and Subscriptions.

Remove IAM Roles and Users.
