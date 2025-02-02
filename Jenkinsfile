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
                        docker build -t mmanoj2002/lms-webapp:latest api/
                        docker push mmanoj2002/lms-webapp:latest
                        docker logout
                        """
                    }
                }
        }
        stage('Docker Build and Push Webapp For LMS') {
            steps {
                sh "echo $USER"
                sh "docker build -t mmanoj2002/lms-webapp:latest webapp/"
                sh "docker push mmanoj2002/lms-webapp:latest"
            }
        }
    }
}