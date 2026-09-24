pipeline {
    agent any

    environment {
        NEXUS_URL = "nexus:8082"
        IMAGE_NAME = "helloapp"
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
                        credentialsId: 'nexus-credentials',
                        usernameVariable: 'NEXUS_USERNAME',
                        passwordVariable: 'NEXUS_PASSWORD'
                    )
                ]) {
                    sh "echo ${NEXUS_PASSWORD} | docker login ${NEXUS_URL} --username '$NEXUS_USERNAME' --password-stdin"
                }
            }
        } 

        stage('Push Image'){
            steps {
                sh "docker push ${NEXUS_URL}/${IMAGE_NAME}:${IMAGE_TAG}"
            }
        }  
    }

    post {
        always {
            sh "docker logout ${NEXUS_URL} || true"
        }
    }
}