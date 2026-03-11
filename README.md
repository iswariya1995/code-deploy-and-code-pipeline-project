# AWS CI/CD Pipeline: Automated EC2 Deployment with CodeDeploy & S3

This project demonstrates a robust Continuous Integration and Continuous Delivery (CI/CD) pipeline. It automates the deployment of a web application from a developer environment to a production EC2 instance using S3 as a source repository and SNS for real-time notifications.

---

## 🏗️ Architectural Design

The architecture is designed for high visibility and automation, ensuring that code changes are deployed safely and consistently.
# AWS CI/CD Pipeline: Automated Web Deployment with CodeDeploy & S3

This project demonstrates a fully automated **Continuous Integration and Continuous Delivery (CI/CD)** pipeline. It enables developers to push code changes to an S3 bucket and have them automatically deployed to a fleet of Amazon Linux 2 web servers via AWS CodeDeploy, with real-time status updates via SNS.

---

### Workflow Logic:
1.  **Source Layer:** A developer pushes a versioned `.zip` artifact to **Amazon S3**.
2.  **Orchestration Layer:** **AWS CodePipeline** detects the new S3 object version and triggers the deployment stage.
3.  **Deployment Layer:** **AWS CodeDeploy** pulls the artifact to the target EC2 instance. It follows the lifecycle hooks defined in the `appspec.yml` to install and restart the Apache web server.
4.  **Feedback Loop:** **Amazon SNS** sends automated alerts (Email/SMS) regarding the status of the deployment (Success/Failure).

---

## 🚀 Implementation Steps

### Step 1: IAM Roles Configuration
To allow services to communicate, two primary roles are required:
* **s3-ec2-full:** Grants the EC2 instance permission to pull code from S3.
* **Code-Deploy Role:** Allows the CodeDeploy service to manage EC2 deployments and interact with related AWS services.

### Step 2: Developer Environment Setup
* Create an IAM user for the developer with programmatic access and download the `.csv` credentials.
* Launch an Amazon Linux 2 EC2 instance (**Dev Machine**).
* Configure the AWS CLI on this machine using the developer credentials to push code artifacts.

### Step 3: Web Server & CodeDeploy Agent
* Launch the target Amazon Linux 2 EC2 instance (**Web Server**).
* Associate the `s3-ec2-full` IAM role with this instance.
* Install the CodeDeploy agent via the terminal:
    ```bash
    sudo yum update -y
    sudo yum install ruby wget -y
    wget [https://aws-codedeploy-us-east-1.s3.amazonaws.com/latest/install](https://aws-codedeploy-us-east-1.s3.amazonaws.com/latest/install)
    chmod +x install
    sudo ./install auto
    sudo service codedeploy-agent status
    ```

### Step 4: Code Preparation & AppSpec
The `appspec.yml` is the core deployment instruction file. It manages the following lifecycle hooks:
* **BeforeInstall:** Executes scripts to install the `httpd` (Apache) dependencies.
* **ApplicationStart:** Starts the Apache service.
* **ApplicationStop:** Gracefully shuts down services before updating files to prevent conflicts.

### Step 5: S3 Integration & Pipeline Trigger
* Create an S3 bucket (e.g., `aws24092021`).
* **Critical Step:** Enable **Bucket Versioning** to allow CodePipeline to detect new uploads.
* Push the application using the AWS CLI:
    ```bash
    aws deploy push --application-name sampleapp --s3-location s3://your-bucket-name/sampleapp.zip
    ```
* Set up **AWS CodePipeline** to monitor the S3 bucket and automate the transition to CodeDeploy, skipping the build stage.

### Step 6: SNS Notifications
* Create an SNS Topic (e.g., `mytopic`).
* Create **Subscriptions** for both **Email** and **SMS**.
* Configure CodeDeploy notification rules to alert the team on deployment `SUCCESS` or `FAILURE`.

---

## 🏁 Conclusion

By implementing this pipeline, the transition from manual, error-prone deployments to an automated workflow was achieved.

* **Automation:** Code moves from development to production without manual console intervention.
* **Consistency:** Using `appspec.yml` and lifecycle scripts ensures the server environment is configured identically during every release.
* **Reliability:** S3 Versioning allows for easy identification of revisions, while **SNS alerts** provide instant operational feedback via mobile and email.



---

## 🧹 Cleanup (Demolish)

To prevent unnecessary AWS costs, remove resources in the following order:

1.  **EC2 Instances:** Terminate the Dev Machine and Web Server.
2.  **Deployment:** Delete the CodePipeline and CodeDeploy Applications/Groups.
3.  **Storage:** Empty and delete the S3 Bucket.
4.  **Alerts:** Delete the SNS Topic and Subscriptions.
5.  **Security:** Remove the IAM Roles and the Developer User.
