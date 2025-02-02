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
                sh "docker build -t mmanoj2002/lms-api:latest api/"
                sh "docker push mmanoj2002/lms-api:latest"
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