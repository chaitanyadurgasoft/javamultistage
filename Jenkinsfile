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
        stage('OWASP Scan'){
            steps {
            dependencyCheck additionalArguments: '--scan .', odcInstallation: 'default'
            }
        }
    }
}
