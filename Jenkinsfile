pipeline {
    agent any

    environment {
        NEXUS_URL = "nexus:8082"
        IMAGE_NAME = "irobust/helloapp"
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout changelog: false, poll: false, scm: scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/irobust/jenkins-example']])
            }
        }
        
        stage('Build docker image') {
            steps {
                sh 'docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .'
            }
        }

        stage('Login to Nexus'){
            steps{
                withCredentials([
                    usernamePassword(
                        credentialsId: 'docker-hub-credentials',
                        usernameVariable: 'NEXUS_USERNAME',
                        passwordVariable: 'NEXUS_PASSWORD'
                    )
                ]) {
                    sh "echo ${NEXUS_PASSWORD} | docker login --username '$NEXUS_USERNAME' --password-stdin"
                }
            }
        } 

        stage('Push Image'){
            steps {
                sh "docker push ${IMAGE_NAME}:${IMAGE_TAG}"
            }
        }  
    }

    post {
        always {
            sh "docker logout ${NEXUS_URL} || true"
        }
    }
}