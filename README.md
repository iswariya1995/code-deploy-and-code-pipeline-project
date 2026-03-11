# 🚀 AWS CI/CD Pipeline: Automated Web Deployment

This project demonstrates a complete **Continuous Integration/Continuous Deployment (CI/CD)** pipeline using AWS. It automates the workflow of deploying a web application from a developer environment to a fleet of EC2 instances.



---

## ## 🎯 Project Objective
To build an automated pipeline where code changes in a repository (S3) are automatically detected and deployed to a web server environment with real-time notifications.

### Core Services
* **AWS CodePipeline**: Orchestrates the workflow.
* **AWS CodeDeploy**: Automates deployment to EC2.
* **Amazon S3**: Artifact store and source repository.
* **Amazon EC2**: Target web servers (Amazon Linux 2).
* **AWS IAM**: Security roles and permissions.
* **Amazon SNS**: Email/SMS alerts for deployment status.

---

## ## 🛠 1. Prerequisites & Setup

### IAM Permissions
Two critical roles are required:
* **EC2-S3-CodeDeploy**: Attach `AmazonS3FullAccess` to allow EC2 to pull code.
* **Code-Deploy-Role**: Use the `CodeDeploy` service role template to manage EC2.

### Environment Preparation
Install the **CodeDeploy Agent** on your target web server:
```bash
sudo yum update -y
sudo yum install ruby wget -y
wget [https://aws-codedeploy-us-east-1.s3.amazonaws.com/latest/install](https://aws-codedeploy-us-east-1.s3.amazonaws.com/latest/install)
chmod +x install
sudo ./install auto
sudo service codedeploy-agent status
