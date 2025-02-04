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

    stages {
        stage('Install Packages') {
            steps {
                container('alpine') {
                    script {
                        sh 'echo "nameserver 8.8.8.8" > /etc/resolv.conf'
                        sh 'apk update'
                        sh 'apk add --no-cache jq kubectl git vim iputils busybox-extras openrc curl bash wget docker-cli dhcpcd'
                        sh 'apk add docker'
                        sh 'rc-update add docker boot'
                        sh 'mkdir -p /run/openrc && touch /run/openrc/softlevel && rc-update add devfs && rc-update add dmesg && openrc'
                        sh 'rc-service dhcpcd start'
                        sh 'service docker start || true'
                        sh 'sleep 5'
                        sh 'docker ps'
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
                            """
                        }
                    }
                }
            }
        }
    }
}
