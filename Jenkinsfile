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
                sh 'docker build -t ${NEXUS_URL}/${IMAGE_NAME}:${IMAGE_TAG} .'
            }
        }
    }
}