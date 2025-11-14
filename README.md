## Install Jenkins locally(Ubuntu)

```bash
sudo apt update
sudo apt install fontconfig openjdk-21-jre
java -version
```
You can enable the Jenkins service to start at boot with the command and run jenkins:

```bash
sudo systemctl enable jenkins
```
check - http://localhost:8080

## Create Docker Hub Access Token

1.Go to Docker Hub and login

2.Click on your profile icon (top right) → Account Settings

3.Go to Security → New Access Token

4.Give it a name (example: "jenkins-token")

5.Click Generate

6.COPY THE TOKEN (you'll only see it once!) - Save it somewhere safe

## Add Credentials to Jenkins

1.Open Jenkins: http://localhost:8080

2.Click Manage Jenkins (left sidebar)

3.Click Manage Credentials

4.Click (global) domain

5.Click Add Credentials (left side)

6.Fill in:

● Kind: Username with password

● Username: Your Docker Hub username 

● Password: Paste the token you copied from Docker Hub

● ID: dockerhub-credentials (this is important! We'll use this in Jenkinsfile)

● Description: Docker Hub Token

## Jenkinsfile

```bash
pipeline {
    agent any
    
    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials')
    }
    
    stages {
        stage('SCM Checkout') {
            steps {
                retry(3) {
                    git branch: 'main', url:'https://github.com/RavinduRasara/CICD-Jenkins-Docker-Github-1'
                }
            }
        }
        
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t #####/docker-cuban:v1.0 .'
            }   
        }
        
        stage('Login to Docker Hub') {
            steps {
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }
        
        stage('Push Image'){
            steps{
                sh 'docker push #####/docker-cuban:v1.0'
            }
        }
    }
    
    post {
        always {
            sh 'docker logout'
        }
    }
}
```

What This Does (Simple Explanation):

**environment**: Loads your Docker Hub credentials that you saved in Jenkins

**DOCKERHUB_CREDENTIALS_USR**: Automatically gets your username

**DOCKERHUB_CREDENTIALS_PSW**: Automatically gets your token/password

**echo $DOCKERHUB_CREDENTIALS_PSW | docker login...**: Logs into Docker Hub securely

post always: Always logout after pipeline finishes (security best practice)

## DockerFile

```bash
FROM node:latest
WORKDIR /usr/src/app
COPY nodeapp/* ./
RUN npm install
EXPOSE 3000
CMD [ "npm", "start" ]
```
## Give Jenkins Permission to Run Docker
Run these commands in your Ubuntu terminal:
```bash
# Add jenkins user to docker group
sudo usermod -aG docker jenkins

# Restart Jenkins
sudo systemctl restart jenkins

# Restart Docker (optional, but recommended)
sudo systemctl restart docker

# Switch to jenkins user
sudo su - jenkins

# Try running docker command
docker ps
```






