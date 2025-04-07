pipeline {
    agent {
        label 'Dev'
    }
    stages {
        stage('Checkout') {
            steps {
                echo '🔄 Cloning repository...'
                checkout scm
            }
        }

        stage('Check Docker') {
            steps {
                script {
                    echo '🔍 Checking Docker installation...'
                    try {
                        sh 'sudo docker --version'
                        sh 'sudo docker ps'
                    } catch (e) {
                        error "❌ Docker is not installed or not running."
                    }
                }
            }
        }

        stage('Docker Build') {
            steps {
                script {
                    echo '🐳 Building Docker image...'
                    try {
                        sh 'sudo docker build -t javaapp:v17 -f multistageDockerfile .'
                        sh 'sudo docker images'
                    } catch (e) {
                        error '❌ Docker image build failed. Check the logs.'
                    }
                }
            }
        }
    }
}
