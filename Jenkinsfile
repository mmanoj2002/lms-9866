pipeline {
    agent none  // Disable global agent so we can specify pod templates per stage
    stages {
        stage('Checkout Code') {
            agent {
                kubernetes {
                    label 'kubeagent'  // This will use a Kubernetes pod template with the label 'git'
                    defaultContainer 'git'  // Container with Git installed
            }
            steps {
                checkout([ 
                    $class: 'GitSCM', 
                    branches: [[name: '*/qa']],  // Replace 'main' with your branch 
                    userRemoteConfigs: [[url: 'https://github.com/mmanoj2002/lms-9866.git']]
                ])
            }
        }
        stage('Docker Build and Push Backend API For LMS') {
            agent {
                kubernetes {
                    label 'docker'  // This will use a Kubernetes pod template with the label 'docker'
                    defaultContainer 'docker'  // Container with Docker installed
            }
            steps {
                // Docker commands
                sh "docker build -t mmanoj2002/lms-api:latest api/"
                sh "docker images"
            }
        }
    }
}
    }
}
