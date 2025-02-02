pipeline {
    agent {
        kubernetes {
            label 'docker'
            inheritFrom 'docker-agent'
            defaultContainer 'docker'
            yaml """
apiVersion: v1
kind: Pod
metadata:
  labels:
    app: lms-9866
spec:
  containers:
  - name: docker
    image: 'docker:latest'
    command:
    - cat
    tty: true
    resources:
      limits:
        memory: "4Gi"
        cpu: "2"
      requests:
        memory: "2Gi"
        cpu: "1"
  serviceAccountName: default
"""
        }
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
                sh "docker images"
            }
        }
    }
}
