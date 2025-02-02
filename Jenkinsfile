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
                        sh """
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        VERSION=$(jq -r '.version' package.json)
                        docker build -t mmanoj2002/lms-api:$VERSION api/
                        docker push mmanoj2002/lms-api:$VERSION
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
                        sh """
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        VERSION=$(jq -r '.version' package.json)
                        docker build -t mmanoj2002/lms-web-app:$VERSION webapp/
                        docker push mmanoj2002/lms-web-app:$VERSION
                        docker logout
                        """
                    }
                }
            }
        }
    }
}