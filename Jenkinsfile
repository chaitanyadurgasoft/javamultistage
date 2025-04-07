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
                        def imageTag = "javaapp:v17${env.BUILD_NUMBER}"
                        sh "sudo docker build -t ${imageTag} -f multistageDockerfile ."
                        sh "sudo docker images"
                    } catch (e) {
                        error '❌ Docker image build failed. Check the logs.'
                    }
                }
            }
        }
        stage('Push to Harbor') {
            steps {
                script {
                    echo 'Tagging and pushing Docker image to Harbor...'
                    try {
                         def localTag = "javaapp:v17${env.BUILD_NUMBER}"
                        def remoteTag = "harbor.testcgit.xyz/myproject/javaapp:v17${env.BUILD_NUMBER}"
                        // Tag the image for your Harbor project
                        sh "sudo docker tag ${localTag} ${remoteTag}"

                        // Login using stored Jenkins credentials
                        withCredentials([usernamePassword(credentialsId: 'harbor-creds', usernameVariable: 'HARBOR_USER', passwordVariable: 'HARBOR_PASS')]) {
                            sh 'echo $HARBOR_PASS | sudo docker login harbor.testcgit.xyz -u $HARBOR_USER --password-stdin'
                        }

                        // Push to Harbor
                        sh "sudo docker push ${remoteTag}"

                        // Optional: logout
                        sh 'sudo docker logout harbor.testcgit.xyz'
                    } catch (e) {
                        error 'Failed to push Docker image to Harbor.'
                    }
                }
            }
        }
        stage('Update values.yaml with image tag') {
            steps {
                script {
                    def newTag = "v17${env.BUILD_NUMBER}"
        
                    // Clean clone the Helm repo into a subdirectory
                    sh "rm -rf helmrepo"
                     sh "git clone -b master https://github.com/chaitanyadurgasoft/helmjavarepo.git helmrepo"
                    dir('helmrepo') {
        
                        // Update the tag in values.yaml
                        sh """
                        sed -i 's/^  tag: .*/  tag: ${newTag}/' values.yaml
                        """
        
                        // Commit & push the change
                        withCredentials([usernamePassword(credentialsId: 'git-creds-helm', usernameVariable: 'GIT_USER', passwordVariable: 'GIT_PASS')]) {
                            sh """
                            git config user.name "jenkins"
                            git config user.email "jenkins@example.com"
                            git add values.yaml
                            git commit -m "Update image tag to ${newTag}"
                            git push https://${GIT_USER}:${GIT_PASS}@github.com/chaitanyadurgasoft/helmjavarepo.git HEAD:master
                            """
                        }
                    }
                }
            }
        }
    }
}
