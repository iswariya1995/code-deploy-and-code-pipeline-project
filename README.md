# AWS CI/CD Pipeline: Automated Web Deployment



## 📌 Project Overview
This project implements a robust **Continuous Integration and Continuous Deployment (CI/CD)** pipeline using AWS native services. It automates the lifecycle of a web application, from source code updates in **Amazon S3** to automated deployment on **Amazon EC2** instances using **AWS CodeDeploy**, all orchestrated by **AWS CodePipeline**.

## 🏗 Architecture
* **Source:** Amazon S3 (Versioned)
* **Orchestration:** AWS CodePipeline
* **Deployment:** AWS CodeDeploy (In-place deployment)
* **Compute:** Amazon EC2 (Amazon Linux 2)
* **Notifications:** Amazon SNS (Email & SMS alerts)

---

## 📂 Project Structure
To ensure successful deployment, the repository is organized as follows:

```plaintext
.
├── index.html          # Main web application file
├── appspec.yml         # AWS CodeDeploy configuration file
├── scripts/            # Lifecycle event hooks
│   ├── httpd_install.sh
│   ├── httpd_start.sh
│   └── httpd_stop.sh
└── README.md           # Project documentation
## Step-by-Step Implementation
Phase 1: Identity & Access Management (IAM)
Before configuring services, we must establish the necessary permissions.

EC2 Service Role: Create a role (e.g., s3-ec2-full) with AmazonS3FullAccess. This allows the web server to pull the application code from S3.

CodeDeploy Service Role: Create a role (e.g., cdrole) using the CodeDeploy service type. This allows CodeDeploy to manage EC2 instances on your behalf.

Developer User: Create an IAM user with programmatic access for your local developer machine and attach the necessary S3 and CodeDeploy permissions.

Phase 2: Web Server Environment Setup
Launch EC2: Start an Amazon Linux 2 instance.

Attach IAM Role: Assign the s3-ec2-full role created in Phase 1.

Install CodeDeploy Agent:

Bash
sudo yum update -y
sudo yum install ruby wget -y
wget https://aws-codedeploy-us-east-1.s3.amazonaws.com/latest/install
chmod +x install
sudo ./install auto
sudo service codedeploy-agent status
Phase 3: Application Development & AppSpec
On your Developer Machine, structure your application as follows:

Plaintext
/deploy_dir
└── /sampleapp
    ├── index.html
    ├── appspec.yml
    └── /scripts
        ├── httpd_install.sh
        ├── httpd_start.sh
        └── httpd_stop.sh
Key File: appspec.yml
This file tells CodeDeploy where to place the files and which scripts to run during the deployment hooks (BeforeInstall, ApplicationStop, etc.).

Phase 4: S3 & Manual Deployment
Create S3 Bucket: Enable Bucket Versioning (required for CodePipeline).

Push Code to S3:

Bash
aws deploy push --application-name sampleapp --s3-location s3://your-bucket-name/sampleapp.zip
Create Deployment Group: In the CodeDeploy console, create a group targeting your EC2 instance (using Tags like AppName: SampleApp).

Execute Deployment: Manually trigger a deployment to verify the appspec.yml and scripts are functioning correctly.

Phase 5: Automating with CodePipeline
Create Pipeline: Link the S3 source bucket to the CodeDeploy application.

Trigger Mechanism: Set the pipeline to trigger every time a new version of sampleapp.zip is detected in S3.

Test Automation: Update index.html on the dev machine, re-zip, and push to S3. The pipeline will automatically detect the change and update the web server.

Phase 6: Notifications (SNS)
Create SNS Topic: Set up a topic (e.g., mytopic).

Subscriptions: Add your Email and Phone Number as protocols.

Configure Triggers: In CodeDeploy, create a notification rule to send alerts on SUCCESS or FAILURE states.

🧹 Clean Up (Avoid Charges)
To prevent unexpected AWS costs, delete resources in this order:

AWS CodePipeline

AWS CodeDeploy Application

EC2 Instances

S3 Bucket & Objects

SNS Topics & Subscriptions

IAM Roles & Users

🛠 Tech Stack
Platform: AWS

Infrastucture: EC2, S3

CI/CD: CodeDeploy, CodePipeline

Security: IAM

Scripting: Bash, YAML

