# meeting-app
project live meet

🔹 Prerequisites
1.	Jenkins installed (Ubuntu/Amazon Linux)
2.	Git installed on Jenkins server
3.	Apache or Nginx installed on Jenkins server
4.	GitHub repository with HTML files
Example:
5.	index.html
6.	style.css
7.	images/
🔹 Step 1: Install Apache (Deployment Server)
sudo apt update
sudo apt install apache2 -y
sudo systemctl start apache2
sudo systemctl enable apache2
Website root:
/var/www/html/
🔹 Step 3: Jenkinsfile (CI + CD)
Create a file called Jenkinsfile in your GitHub repo 👇
pipeline {
    agent any

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/USERNAME/REPO_NAME.git'
            }
        }

        stage('Build') {
            steps {
                echo "No build needed for static HTML"
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                sudo rm -rf /var/www/html/*
                sudo cp -r * /var/www/html/
                '''
            }
        }
    }

    post {
        success {
            echo "Website deployed successfully!"
        }
        failure {
            echo "Deployment failed!"
        }
    }
}
🔹 Replace:
•	USERNAME
•	REPO_NAME
•	branch if not main
🔹 Step 4: Give Jenkins Permission (IMPORTANT)
Apache folder needs sudo access.
Run:
sudo visudo
Add at bottom:
jenkins ALL=(ALL) NOPASSWD: /bin/rm, /bin/cp
🔹 Step 5: Configure Pipeline in Jenkins
1.	Go to Pipeline
2.	Definition → Pipeline script from SCM
3.	SCM → Git
4.	Repository URL → GitHub repo
5.	Branch → main
6.	Script Path → Jenkinsfile
7.	Save
🔹 Step 6: Build Now
Click Build Now
After success, open browser:
http://<jenkins-server-ip>
🎉 Your HTML website is LIVE!
🔹 Optional Enhancements
•	GitHub Webhook (Auto deploy on push)
•	Nginx instead of Apache
•	Deploy to EC2 / VM
•	Add email/Slack notification

On Jenkins Server
Install required packages:
sudo apt update
sudo apt install -y nginx git
Start Nginx:
sudo systemctl start nginx
sudo systemctl enable nginx
Your website will be deployed here:
/var/www/html
In Jenkins Dashboard → Manage Jenkins → Plugins, install:
✅ Git
✅ GitHub
✅ GitHub Integration
✅ Pipeline
Restart Jenkins if required.
1.	Jenkins Dashboard → New Item
2.	Job Name: html-ci-cd
3.	Select Pipeline
4.	Click OK
Use this simple pipeline 👇
pipeline {
    agent any

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/USERNAME/REPO_NAME.git'
            }
        }

        stage('Validate HTML') {
            steps {
                sh '''
                echo "Checking HTML files..."
                ls -l
                '''
            }
        }

        stage('Deploy to Server') {
            steps {
                sh '''
                sudo rm -rf /var/www/html/*
                sudo cp -r * /var/www/html/
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Website deployed successfully"
        }
        failure {
            echo "❌ Deployment failed"
        }
    }
}
📌 Replace:
•	USERNAME
•	REPO_NAME
•	branch if using master
Allow Jenkins to Deploy (Important)
Jenkins needs sudo access to copy files.
Run:
sudo visudo
Add this line at the end:
jenkins ALL=(ALL) NOPASSWD: ALL
Restart Jenkins:
sudo systemctl restart Jenkins

Configure GitHub Webhook (Auto Deploy)
In GitHub Repository:
1.	Go to Settings → Webhooks
2.	Click Add webhook
Fill details:
•	Payload URL
http://JENKINS_IP:8080/github-webhook/
•	Content type: application/json
•	Which events? → Just push
•	✔ Active
Click Add webhook
 Enable Webhook Trigger in Jenkins
In Jenkins Job:
1.	Configure
2.	Under Build Triggers
3.	✅ Check:
GitHub hook trigger for GITScm polling
4.	Save
Test the CI/CD Pipeline
Push Code to GitHub:
git add .
git commit -m "update html site"
git push origin main
🎉 Jenkins will:
•	Automatically trigger
•	Pull code
•	Deploy HTML
•	Update website instantly
Access Your Website
Open browser:
http://SERVER_IP
✅ Result
✔ Auto deployment
✔ CI/CD enabled
✔ No manual Jenkins build
✔ Simple & production-ready for static websites


🔹 Architecture (Simple & Real)
GitHub (HTML code)
   ↓
Jenkins (CI)
   ↓
Build + Test
   ↓
Deploy to Server (CD)
We’ll deploy the HTML site to Apache (httpd) on a Linux server.
🔹 Prerequisites
Make sure you have:
•	Jenkins installed & running
•	Git installed on Jenkins server
•	Apache (httpd) installed on target server (can be same Jenkins server)
•	GitHub repo with simple HTML
Example:
index.html
style.css
🔹 Step 1: Install Apache (if not installed)
sudo apt update
sudo apt install apache2 -y
sudo systemctl start apache2
sudo systemctl enable apache2
Apache default path:
/var/www/html
🔹 Step 2: Create Jenkins Job
1.	Open Jenkins Dashboard
2.	Click New Item
3.	Name: html-ci-cd-pipeline
4.	Select Pipeline
5.	Click OK
🔹 Step 3: Jenkinsfile (CI + CD)
Create a file named Jenkinsfile in your GitHub repo 👇
pipeline {
    agent any

    stages {

        stage('Clone Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/USERNAME/REPO_NAME.git'
            }
        }

        stage('Build') {
            steps {
                echo "No build needed for static HTML"
            }
        }

        stage('Test') {
            steps {
                sh '''
                if [ -f index.html ]; then
                  echo "index.html exists - Test Passed"
                else
                  echo "index.html missing - Test Failed"
                  exit 1
                fi
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                sudo cp -r * /var/www/html/
                sudo systemctl restart apache2
                '''
            }
        }
    }

    post {
        success {
            echo "🎉 Deployment Successful!"
        }
        failure {
            echo "❌ Pipeline Failed!"
        }
    }
}
⚠️ Replace:
USERNAME
REPO_NAME
🔹 Step 4: Jenkins Permissions (IMPORTANT)
Allow Jenkins to use sudo without password:
sudo visudo
Add:
jenkins ALL=(ALL) NOPASSWD: ALL

🔹 Step 5: Configure Pipeline Job
In Jenkins job:
•	Go to Pipeline
•	Select: Pipeline script from SCM
•	SCM: Git
•	Repo URL: https://github.com/USERNAME/REPO_NAME.git
•	Branch: main
•	Script Path: Jenkinsfile
•	Save 
🔹 Step 6: Run Pipeline
Click Build Now 🎯
If successful:
•	Open browser:
http://<jenkins-server-ip>
👉 Your HTML site will be live! 🎉🔥
🔹 CI vs CD (Easy Understanding)
Stage	What Happens
CI	Code clone + test
CD	Deploy to Apache
Below is a complete, simple CI/CD setup for a static HTML website from GitHub, with:
✅ GitHub Webhook (auto trigger on push)
✅ Jenkins CI pipeline
✅ Code quality check using SonarQube
✅ Docker image build
✅ Deploy to Kubernetes cluster using NGINX
This is industry-standard and works perfectly for a simple HTML project.
________________________________________
Architecture Flow (Simple)
GitHub (HTML code)
   ↓ (Webhook)
Jenkins
   ├── Code Checkout
   ├── SonarQube Code Scan
   ├── Build Docker Image (Nginx + HTML)
   ├── Push Image to DockerHub
   └── Deploy to Kubernetes (Nginx Pod)
________________________________________
Prerequisites
On Jenkins Server
Install:
•	Jenkins
•	Docker
•	kubectl
•	Java (for Jenkins & Sonar)
•	SonarQube server running (local or VM)
Jenkins Plugins
Install these plugins:
•	Git
•	Pipeline
•	Docker Pipeline
•	Kubernetes CLI
•	SonarQube Scanner
________________________________________
Sample GitHub Project Structure
html-app/
│── index.html
│── Dockerfile
│── deployment.yaml
│── Jenkinsfile
________________________________________
Dockerfile (NGINX + HTML
FROM nginx:latest
COPY . /usr/share/nginx/html
________________________________________
 Kubernetes Deployment (deployment.yaml)
apiVersion: apps/v1
kind: Deployment
metadata:
  name: html-nginx
spec:
  replicas: 2
  selector:
    matchLabels:
      app: html
  template:
    metadata:
      labels:
        app: html
    spec:
      containers:
      - name: nginx
        image: yourdockerhubusername/html-app:latest
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: html-service
spec:
  type: NodePort
  selector:
    app: html
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30080
________________________________________
SonarQube Configuration
In Jenkins
1.	Manage Jenkins → System
2.	Add SonarQube Server
o	Name: sonar-server
o	URL: http://SONAR_IP:9000
o	Token: (create in SonarQube)
Sonar Scanner
•	Manage Jenkins → Tools
•	Add SonarScanner
________________________________________
jenkinsfile (CI + CD Pipeline)
pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "yourdockerhubusername/html-app"
        SONARQUBE_ENV = "sonar-server"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/yourusername/html-app.git'
            }
        }

        stage('SonarQube Code Scan') {
            steps {
                withSonarQubeEnv("${SONARQUBE_ENV}") {
                    sh '''
                    sonar-scanner \
                    -Dsonar.projectKey=html-app \
                    -Dsonar.sources=.
                    '''
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t $DOCKER_IMAGE:latest ."
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS'
                )]) {
                    sh '''
                    docker login -u $USER -p $PASS
                    docker push $DOCKER_IMAGE:latest
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                kubectl apply -f deployment.yaml
                kubectl rollout status deployment/html-nginx
                '''
            }
        }
    }
}
________________________________________
GitHub Webhook Setup (Auto Deploy)
In GitHub Repo
1.	Settings → Webhooks → Add Webhook
2.	Payload URL:
http://JENKINS_IP:8080/github-webhook/
3.	Content type: application/json
4.	Events: Just push
5.	Save
________________________________________
jenkins Job Setup
1.	New Item → Pipeline
2.	Pipeline Definition:
👉 Pipeline script from SCM
3.	SCM: Git
4.	Repo URL: GitHub repo
5.	Branch: main
6.	Save
________________________________________
Access Your App
http://<K8S_NODE_IP>:30080
Every Git push → automatic deploy 🚀
