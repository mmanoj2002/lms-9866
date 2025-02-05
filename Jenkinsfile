pipeline {
    agent {
        kubernetes {
            yaml """
apiVersion: v1
kind: Pod
metadata:
  labels:
    app: lms
spec:
  hostNetwork: true
  dnsPolicy: ClusterFirstWithHostNet
  containers:
  - name: alpine
    image: alpine:latest
    securityContext:
      privileged: true
    command:
    - cat
    tty: true
"""
        }
    }

    environment {
        KUBECONFIG = "$WORKSPACE/k8s-sa-token"
    }

    stages {
        stage('Install Packages') {
            steps {
                container('alpine') {
                    script {
                        sh 'apk update && apk add --no-cache jq kubectl git curl bash wget aws-cli'
                    }
                }
            }
        }

        stage('Checkout Code') {
            steps {
                container('alpine') {
                    retry(3) {
                        sh 'git clone -b qa https://github.com/mmanoj2002/lms-9866.git'
                    }
                }
            }
        }

        stage('Access AWS and Kubernetes Using AWS CLI') {
            steps {
                container('alpine') {
                    script {
                        withCredentials([string(credentialsId: 'aws-access-key-id', variable: 'AWS_ACCESS_KEY_ID'),
                                         string(credentialsId: 'aws-secret-access-key', variable: 'AWS_SECRET_ACCESS_KEY')]) {
                            sh '''
                            # Set the AWS region
                            export AWS_DEFAULT_REGION="ap-south-1"

                            # Export AWS credentials
                            export AWS_ACCESS_KEY_ID="$AWS_ACCESS_KEY_ID"
                            export AWS_SECRET_ACCESS_KEY="$AWS_SECRET_ACCESS_KEY"

                            # Update kubeconfig for EKS cluster
                            aws eks update-kubeconfig --region $AWS_DEFAULT_REGION --name LMS-App

                            # Verify Kubernetes connection
                            kubectl get pods
                            '''
                        }
                    }
                }
            }
        }

        stage('Docker Build and Push Backend API For LMS') {
            steps {
                container('alpine') {
                    script {
                        withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                            def version = sh(script: "jq -r '.version' lms-9866/api/package.json", returnStdout: true).trim()
                            sh """
                            echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                            docker build -t mmanoj2002/lms-api:$version lms-9866/api/
                            docker push mmanoj2002/lms-api:$version
                            docker logout
                            kubectl get pods -n qa
                            """
                        }
                    }
                }
            }
        }

        stage('Docker Build and Push Webapp For LMS') {
            steps {
                container('alpine') {
                    script {
                        withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                            def version = sh(script: "jq -r '.version' lms-9866/webapp/package.json", returnStdout: true).trim()
                            sh """
                            echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                            docker build -t mmanoj2002/lms-web-app:$version lms-9866/webapp/
                            docker push mmanoj2002/lms-web-app:$version
                            docker logout
                            kubectl get pods -n qa
                            """
                        }
                    }
                }
            }
        }
    }
}
