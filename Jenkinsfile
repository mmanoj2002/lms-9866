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
                sh "curl -sL https://deb.nodesource.com/setup_16.x | bash -
                sh "apt-get upadte && apt-get install -y nodejs"
            }
        }
    }
}
