pipeline {
    agent any
    tools {
        maven 'Maven_3.0.5'
    }
    stages {
        stage('Clone the repo from github') {
            steps {
                git branch: 'pushing-docker-image-to-dockerhub-jenkinsfile', credentialsId: 'github-jenkins', url: 'https://github.com/madinenitejaswini/java-application.git'
            }
        }
        stage('Build the code') {
            steps {
                sh 'mvn clean install'
            }
        }
        stage('Build Docker Image') {
            steps {
                sh '''
                docker build . -t web-application:$BUILD_NUMBER
                docker tag web-application:$BUILD_NUMBER madinenitejaswini/mt:$BUILD_NUMBER
                '''  
            }
        }
        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'DockerHub_Credentials', passwordVariable: 'DOCKERHUB_PASSWORD', usernameVariable: 'DOCKERHUB_USERNAME')]) {
                    sh '''
                    docker login -u $DOCKER_HUB_USERNAME -p $DOCKER_HUB_PASSWORD
                    docker push madinenitejaswini/mt:$BUILD_NUMBER
                    '''
                }
            }
        }
    }
}
