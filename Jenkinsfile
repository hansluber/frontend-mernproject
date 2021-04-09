env.DOCKER_REGISTRY = 'hansluber'
env.DOCKER_IMAGE_NAME = 'frontend5'
pipeline {
    agent any
    stages {
        stage('Git Pull from Github') {
            steps {
                git url: 'https://github.com/hansluber/frontend-mernproject.git'
            }
        }    
        stage('Build Docker Image') {
            steps {
                sh "docker build -t $DOCKER_REGISTRY/$DOCKER_IMAGE_NAME:${BUILD_NUMBER} ."
            }
        }              
        stage('Push Docker Image to ECR') {
            steps {
                sh "docker push $DOCKER_REGISTRY/$DOCKER_IMAGE_NAME:${BUILD_NUMBER}"
            }
        } 
        stage('Deploy To Kubernetes Cluster') {
            steps {
                sh'''sed -i "15d" frontend-deployment.yml'''
                sh'''sed -i "14 a \'\\'          image: $DOCKER_REGISTRY/$DOCKER_IMAGE_NAME:${BUILD_NUMBER}" frontend-deployment.yml && sed -i "s/''//" frontend-deployment.yml'''
                sh "kubectl apply -f frontend-deployment.yml"                
            }
        }
        stage('Remove Docker Image in local') {
            steps {
                sh "docker rmi $DOCKER_REGISTRY/$DOCKER_IMAGE_NAME:${BUILD_NUMBER}"
            }
        }
        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }        
    }
}
