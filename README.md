# meeting-app
project live meet

🔹 Architecture (Simple & Real)
GitHub (HTML code)
   ↓
Jenkins (CI)
   ↓
Build + Test
   ↓
Deploy to Server (CD)
We’ll deploy the HTML site to Apache (httpd) on a Linux server.
________________________________________
🔹 Prerequisites
Make sure you have:
•	Jenkins installed & running
•	Git installed on Jenkins server
•	Apache (httpd) installed on target server (can be same Jenkins server)
•	GitHub repo with simple HTML
Example:
index.html
style.css
________________________________________
🔹 Step 1: Install Apache (if not installed)
sudo apt update
sudo apt install apache2 -y
sudo systemctl start apache2
sudo systemctl enable apache2
Apache default path:
/var/www/html
________________________________________
🔹 Step 2: Create Jenkins Job
1.	Open Jenkins Dashboard
2.	Click New Item
3.	Name: html-ci-cd-pipeline
4.	Select Pipeline
5.	Click OK
________________________________________
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
________________________________________
🔹 Step 4: Jenkins Permissions (IMPORTANT)
Allow Jenkins to use sudo without password:
sudo visudo
Add:
jenkins ALL=(ALL) NOPASSWD: ALL
________________________________________
🔹 Step 5: Configure Pipeline Job
In Jenkins job:
•	Go to Pipeline
•	Select: Pipeline script from SCM
•	SCM: Git
•	Repo URL: https://github.com/USERNAME/REPO_NAME.git
•	Branch: main
•	Script Path: Jenkinsfile
•	Save ✅
________________________________________
🔹 Step 6: Run Pipeline
Click Build Now 🎯
If successful:
•	Open browser:
http://<jenkins-server-ip>
👉 Your HTML site will be live! 🎉🔥
________________________________________
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
1️⃣ Architecture Flow (Simple)
GitHub (HTML code)
   ↓ (Webhook)
Jenkins
   ├── Code Checkout
   ├── SonarQube Code Scan
   ├── Build Docker Image (Nginx + HTML)
   ├── Push Image to DockerHub
   └── Deploy to Kubernetes (Nginx Pod)
________________________________________
2️⃣ Prerequisites
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
3️⃣ Sample GitHub Project Structure
html-app/
│── index.html
│── Dockerfile
│── deployment.yaml
│── Jenkinsfile
________________________________________
4️⃣ Dockerfile (NGINX + HTML)
FROM nginx:latest
COPY . /usr/share/nginx/html
________________________________________
5️⃣ Kubernetes Deployment (deployment.yaml)
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
6️⃣ SonarQube Configuration
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
7️⃣ Jenkinsfile (CI + CD Pipeline)
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
8️⃣ GitHub Webhook Setup (Auto Deploy)
In GitHub Repo
1.	Settings → Webhooks → Add Webhook
2.	Payload URL:
http://JENKINS_IP:8080/github-webhook/
3.	Content type: application/json
4.	Events: Just push
5.	Save
________________________________________
9️⃣ Jenkins Job Setup
1.	New Item → Pipeline
2.	Pipeline Definition:
👉 Pipeline script from SCM
3.	SCM: Git
4.	Repo URL: GitHub repo
5.	Branch: main
6.	Save
________________________________________
🔟 Access Your App
http://<K8S_NODE_IP>:30080
Every Git push → automatic deploy 🚀




