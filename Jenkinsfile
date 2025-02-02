pipeline {
    agent {
            label 'kubeagent'
    }
    stages {
        stage('Docker Build and Push Backend API For LMS') {
            steps {
                sh "docker build -t mmanoj2002/lms-api:latest api/"
                sh "docker images"
            }
        }
    }
}