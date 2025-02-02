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
                sh "curl -sL https://deb.nodesource.com/setup_16.x | sudo bash -"
                sh "sudo apt-get install -y nodejs"
                sh "pwd && ls -al"
            }
        }
        stage('Build Backend API For LMS') {
            steps {
                sh "cd api && npm install && npm run build"
                sh "ls -al api"
            }
        }
    }
}
