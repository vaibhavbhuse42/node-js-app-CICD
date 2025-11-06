# ⚙️🚀 Node.js CI/CD Pipeline using Jenkins & AWS EC2

## 🧩 Technologies Used
Tool	Purpose
Jenkins	Automate the CI/CD pipeline
Node.js	Backend framework
GitHub	Source code repository
PM2	Node process manager
AWS EC2 (Ubuntu)	Hosting & deployment environment
## ⚙️ Jenkins Setup Process
### 🔑 Step 1: Add SSH Credentials

Go to Manage Jenkins → Credentials → System → Global Credentials

Click Add Credentials

Kind: SSH Username with private key

ID: node-app-key

Username: ubuntu

Private Key: Paste your .pem key content

###  🧱 Step 2: Create Pipeline Job

In Jenkins Dashboard → New Item → Enter name: node-app-deployment

Select Pipeline

Under Pipeline Script from SCM:

SCM: Git

Repository URL: https://github.com/vaibhavbhuse42/node-js-app-CICD.git

Branch: main

Credentials: node-app-key

Save ✅

## 🧾 Jenkinsfile (Pipeline Script)
pipeline {
    agent any

    environment {
        SERVER_IP      = '52.66.239.134'
        SSH_CREDENTIAL = 'node-app-key'
        REPO_URL       = 'https://github.com/vaibhavbhuse42/node-js-app-CICD.git'
        BRANCH         = 'main'
        REMOTE_USER    = 'ubuntu'
        REMOTE_PATH    = '/home/ubuntu/node-app'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: "${BRANCH}", url: "${REPO_URL}"
            }
        }

        stage('Upload Files to EC2') {
            steps {
                sshagent([SSH_CREDENTIAL]) {
                    sh """
                        ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${SERVER_IP} 'mkdir -p ${REMOTE_PATH}'
                        scp -o StrictHostKeyChecking=no -r * ${REMOTE_USER}@${SERVER_IP}:${REMOTE_PATH}/
                    """
                }
            }
        }

        stage('Install Dependencies & Start App') {
            steps {
                sshagent([SSH_CREDENTIAL]) {
                    sh """
                        ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${SERVER_IP} '
                            cd ${REMOTE_PATH} &&
                            npm install &&
                            pm2 start app.js --name node-app || pm2 restart node-app
                        '
                    """
                }
            }
        }
    }

    post {
        success {
            echo '✅ Application deployed successfully!'
        }
        failure {
            echo '❌ Deployment failed.'
        }
    }
}

## 🔁 CI/CD Pipeline Flow
flowchart TD
A[GitHub Repository Push] --> B[Jenkins Pipeline Trigger]
B --> C[Clone Source Code]
C --> D[Upload Files to EC2 via SSH]
D --> E[Install Dependencies (npm install)]
E --> F[Start/Restart App using PM2]
F --> G[Application Running on EC2 ✅]

## 🧰 EC2 Instance Setup (Pre-Requisites)

Before running the Jenkins pipeline, ensure your EC2 instance is configured properly:

sudo apt update -y
sudo apt install -y nodejs npm git
sudo npm install -g pm2


Verify installation:

node -v
npm -v
pm2 -v

## 🌐 Access the Application

Once the deployment is complete, open your app in the browser:

http://52.66.239.134:3000


(Replace the port number if it’s different in your app.js file.)

## 🏁 Pipeline Output
Stage	Description	Status
Clone Repository	Pulls latest code from GitHub	✅
Upload to EC2	Copies project files via SCP	✅
Install & Start	Installs dependencies and runs the app	✅
Final Result	Node.js App deployed successfully	🚀
## 🖼️ Screenshots

Add your screenshots below for better presentation!

🔹 Jenkins Dashboard

### ![Jenkins Dashboard](screenshots/jenkins-dashboard.png)

🔹 ![](/image/Screenshot%20(7).png)

### ![Build Console](screenshots/build-console.png)

🔹 ![](/image/Screenshot%202025-11-05%20165518.png)

### ![EC2 Terminal](screenshots/ec2-terminal.png)

🔹 ![](/image/Screenshot%202025-11-05%20165643.png)

### ![Browser Output](screenshots/browser-output.png)

   ![](/image/Screenshot%202025-11-05%20165418.png)

## 🙌 Credits

👨‍💻 Developed by: Vaibhav Navnath Bhuse
📦 Project Name: Node.js CI/CD Pipeline using Jenkins & EC2
⚙️ Tools Used: Jenkins · Node.js · PM2 · GitHub · AWS EC2
🗓️ Year: 2025