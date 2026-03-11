# AWS CI/CD Pipeline: Automated Deployment with CodeDeploy & S3

This project demonstrates a complete **Continuous Integration and Continuous Delivery (CI/CD)** workflow using AWS native tools. It automates the process of pulling code from a developer environment, storing it in S3, and deploying it to a fleet of EC2 instances with real-time SNS notifications.

---

## 🏗️ Architectural Design

The pipeline follows a **Decoupled Architecture**, ensuring that the developer environment, the artifact storage, and the production web servers are isolated for security and scalability.



### How it works:
1. **Source Layer:** A developer pushes a `.zip` revision to an **S3 Bucket**. **S3 Versioning** acts as the trigger.
2. **Orchestration Layer:** **AWS CodePipeline** detects the change and pulls the latest artifact.
3. **Deployment Layer:** **AWS CodeDeploy** processes the `appspec.yml` file, mapping files to `/var/www/html/` and executing lifecycle hooks (scripts).
4. **Monitoring Layer:** **SNS** provides a feedback loop, sending Email/SMS alerts on deployment states (Success/Failure).

---

## 🛠️ Services & Requirements
| Service | Role |
| :--- | :--- |
| **EC2** | Web Server (Target) and Developer Machine. |
| **S3** | Artifact storage with Versioning enabled. |
| **CodeDeploy** | Manages the deployment lifecycle and EC2 Agent communication. |
| **CodePipeline** | Orchestrates the flow from S3 to Deployment. |
| **IAM** | Provides Least-Privilege access via Roles and Policies. |
| **SNS** | Real-time notifications (Email/SMS). |

---

## 🚀 Step-by-Step Implementation

### 1. IAM Configuration
* **s3-ec2-full Role:** Allows EC2 to pull artifacts from S3.
* **CodeDeployRole:** Allows the CodeDeploy service to manage EC2 instances.
* **Developer User:** IAM user with programmatic access for CLI operations.

### 2. Environment Setup (EC2)
* **Web Server:** Install the **CodeDeploy Agent** on an Amazon Linux 2 instance.
* **Developer Machine:** Configure the AWS CLI to push code.



### 3. Application Structure
Your project directory must include the `appspec.yml` and a `scripts/` folder:
```text
.
├── index.html          # Web content
├── appspec.yml         # Deployment "brain"
└── scripts/            # Lifecycle hooks
    ├── httpd_install.sh
    ├── httpd_start.sh
    └── httpd_stop.sh
## 🛠️ Step 4: Application Development & Lifecycle Configuration

On the **Developer Machine**, we prepare the application artifacts. The `appspec.yml` file is critical as it tells CodeDeploy how to handle the deployment lifecycle.

### 1. Project Structure
Create the following directory structure:
```bash
mkdir -p deploy_dir/sampleapp/scripts
cd deploy_dir/sampleapp
