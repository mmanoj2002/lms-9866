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
                        sh 'apk update && apk add --no-cache jq kubectl git curl bash wget'
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

        stage('Access Kubernetes Using Kubeconfig') {
            steps {
                container('alpine') {
                    withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG_FILE')]) {
                        sh 'cp $KUBECONFIG_FILE $KUBECONFIG'
                        sh 'kubectl config view'
                        sh 'kubectl get pods -n qa'
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
