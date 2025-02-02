pipeline {
    agent {
            label 'kubeagent'
    }
    stages {
        stage('Installed Required Packages in Ubuntu Container') {
            steps {
                sh "sudo apt update"
                sh "sudo apt install git -y"
                sh "sudo apt install docker.io -y"
            }
        }
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
                sh "docker images"
            }
        }
    }
}