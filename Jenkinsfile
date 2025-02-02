pipeline {
    agent none  // Disable the global agent to allow defining per-stage agents
    stages {
        stage('Checkout Code') {
            agent {
                kubernetes {
                    label 'kubeagent'  // Use the preconfigured Kubernetes pod with the label 'kubeagent' for the checkout stage
                    defaultContainer 'git'  // Assuming 'git' container is preconfigured in your Kubernetes pod template
                }
            }
            steps {
                checkout([ 
                    $class: 'GitSCM', 
                    branches: [[name: '*/qa']],  // Replace 'qa' with your branch name
                    userRemoteConfigs: [[url: 'https://github.com/mmanoj2002/lms-9866.git']]
                ])
            }
        }
        stage('Docker Build and Push Backend API For LMS') {
            agent {
                kubernetes {
                    label 'docker'  // Use the preconfigured Kubernetes pod with the label 'docker' for the Docker build stage
                    defaultContainer 'docker'  // Assuming 'docker' container is preconfigured in your Kubernetes pod template
                }
            }
            steps {
                sh "docker build -t mmanoj2002/lms-api:latest api/"
                sh "docker images"
            }
        }
    }
}
