pipeline {
    agent any
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
        stage('Install Packages') {
            steps {
                sh "curl -sL https://get.docker.com | sudo bash -"
                sh "service docker start && service docker status"
                
            }
        }
        stage('Docker Build and Push Backend API For LMS') {
            steps {
                sh "docker build -t mmanoj2002/lms-api:latest ."
                sh "docker images"
            }
        }
    }
}
