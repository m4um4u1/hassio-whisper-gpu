pipeline {

    agent {
        docker {
            label 'docker'                        
            image "git.mausas.de/maurice.hansen/developer-env-forgeops:latest"
            args '-u root:root -v /var/run/docker.sock:/var/run/docker.sock'
            registryUrl 'https://git.mausas.de/'
            registryCredentialsId "GITEA_TOKEN_ID"
            reuseNode true
        }
    }

    environment {
        DESTINATION_REPO = "git.mausas.de/maurice.hansen/hassio-whisper"
        GITEA_TOKEN_ID = "${GITEA_TOKEN_ID}"
    }

    stages {

        stage('Initialization') {
            steps {
                script {
                    currentBuild.displayName = "#${BUILD_NUMBER} - HassioWhisper - latest"
                }
            }

        }

        stage('Build Image') {
            steps {
                script {
                    sh """
                        docker build -t ${DESTINATION_REPO}:latest .
                    """
                }  
            }
        }

        stage('Push Image') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'GITEA_TOKEN_ID', usernameVariable: 'GITEA_USERNAME', passwordVariable: 'GITEA_PASS')]) {
                        sh "docker login git.mausas.de -u ${GITEA_USERNAME} -p ${GITEA_PASS}"    
                        sh "docker push ${DESTINATION_REPO}:latest"
                    }
                }
            }
        }
    }

    post {
        always {
            cleanWs deleteDirs: true, notFailBuild: true
        }
    }

}