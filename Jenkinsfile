
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
                sh 'docker build -t ravi943/docker-cuban:v1.0 .'
            }   
        }
        stage('Login to Docker Hub') {
            steps {
               sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
          }
        
        stage('Push Image'){
            steps{
                sh 'docker push ravi943/docker-cuban:v1.0'
            }
        }
    }
    post {
        always {
            sh 'docker logout'
        }
    }
}
 
