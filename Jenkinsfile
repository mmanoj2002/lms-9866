pipeline {
    agent {
            label 'agent'
    }
    stages {
        stage('Checkout Code') {
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/qa']],  // Replace 'main' with your branch
                    userRemoteConfigs: [[url: 'https://github.com/mmanoj2002/lms-9866.git']]
                ])
            }
        }
        stage('Docker Build and Push Backend API For LMS') {
             steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        def version = sh(script: "jq -r '.version' api/package.json", returnStdout: true).trim()
                        sh """
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker build -t mmanoj2002/lms-api:$version api/
                        docker push mmanoj2002/lms-api:$version
                        docker logout
                        """
                    }
                }
        }
        }
        stage('Docker Build and Push Webapp For LMS') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        def version = sh(script: "jq -r '.version' weapp/package.json", returnStdout: true).trim()
                        sh """
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker build -t mmanoj2002/lms-web-app:$version webapp/
                        docker push mmanoj2002/lms-web-app:$version
                        docker logout
                        """
                    }
                }
            }
        }
    }
}