pipeline {
    agent {
        label 'Dev'
    }
    stages {
        stage('Checkout') {
            steps {
                echo 'Cloning repository...'
                checkout scm
            }
        }
        stage('check Docker') {
            steps {
        script {
            try {
                sh 'sudo docker --version'
                sh 'sudo docker ps'
            } catch (e) {
                error "Docker is not installed or not running."
            }
        }
        }
    }
    }
}