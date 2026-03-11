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
