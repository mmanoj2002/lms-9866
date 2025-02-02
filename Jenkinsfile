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
                sh "apt update && apt install apt-transport-https ca-certificates curl software-properties-common && curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg"
                sh "echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null"
                sh "apt update"
                sh "apt install docker-ce"
                sh "service docker status"
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
