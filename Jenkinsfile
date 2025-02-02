pipeline {
    agent none
    stages {
        stage('Checkout Code') {
            agent {
                kubernetes {
                    label 'kubeagent'
                    defaultContainer 'git'
            }
            steps {
                checkout([ 
                    $class: 'GitSCM', 
                    branches: [[name: '*/qa']],
                    userRemoteConfigs: [[url: 'https://github.com/mmanoj2002/lms-9866.git']]
                ])
            }
        }
        stage('Docker Build and Push Backend API For LMS') {
            agent {
                kubernetes {
                    label 'docker'
                    defaultContainer 'docker'
            }
            steps {
                sh "docker build -t mmanoj2002/lms-api:latest api/"
                sh "docker images"
            }
        }
    }
}
    }
}
