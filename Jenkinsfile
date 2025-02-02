pipeline {
    agent {
        kubernetes {
            label 'docker'  // Label to match the pod template defined in Jenkins
            inheritFrom 'docker-agent'  // Inherit settings from a global pod template called 'docker-agent'
            defaultContainer 'docker'  // Define which container should be used as the default for all steps
            yaml """
apiVersion: v1
kind: Pod
metadata:
  labels:
    app: lms-9866
spec:
  containers:
  - name: docker
    image: 'docker:latest'  // Docker image to use for the container
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
  serviceAccountName: default  // Ensure you have the correct service account if needed
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
