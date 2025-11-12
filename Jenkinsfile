
pipeline {
    agent any

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
               withCredentials([string(credentialsId: 'dockerhub-token', variable: 'DOCKERHUB_TOKEN')]) {
                sh 'echo DOCKERHUB_TOKEN | docker login -u ravi943 --password-stdin'
            }
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
 
