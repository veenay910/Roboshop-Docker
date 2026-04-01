pipeline {
    agent any

    environment {
        DOCKERHUB_USERNAME = "veenay910"
        DOCKERHUB_CREDENTIALS = "dockerhub-creds"  // Jenkins credentials ID
        IMAGE_TAG = "latest"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Detect Changes') {
            steps {
                script {
                    def changes = sh(
                        script: "git diff --name-only ${env.GIT_PREVIOUS_SUCCESSFUL_COMMIT} ${env.GIT_COMMIT}",
                        returnStdout: true
                    ).trim()

                    echo "Changed files:\n${changes}"

                    env.BUILD_DEBUG = "false"
                    env.BUILD_MONGODB = "false"

                    if (changes.contains("debug/")) {
                        env.BUILD_DEBUG = "true"
                    }

                    if (changes.contains("mongodb/")) {
                        env.BUILD_MONGODB = "true"
                    }

                    echo "BUILD_DEBUG: ${env.BUILD_DEBUG}"
                    echo "BUILD_MONGODB: ${env.BUILD_MONGODB}"
                }
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: "${env.DOCKERHUB_CREDENTIALS}",
                    usernameVariable: 'USERNAME',
                    passwordVariable: 'PASSWORD'
                )]) {
                    sh "echo $PASSWORD | docker login -u $USERNAME --password-stdin"
                }
            }
        }

        stage('Build & Push Debug Image') {
            when {
                expression { env.BUILD_DEBUG == "true" }
            }
            steps {
                script {
                    sh """
                        docker build -t ${DOCKERHUB_USERNAME}/debug:${IMAGE_TAG} ./debug
                        docker push ${DOCKERHUB_USERNAME}/debug:${IMAGE_TAG}
                    """
                }
            }
        }

        stage('Build & Push MongoDB Image') {
            when {
                expression { env.BUILD_MONGODB == "true" }
            }
            steps {
                script {
                    sh """
                        docker build -t ${DOCKERHUB_USERNAME}/mongodb:${IMAGE_TAG} ./mongodb
                        docker push ${DOCKERHUB_USERNAME}/mongodb:${IMAGE_TAG}
                    """
                }
            }
        }
    }

    post {
        always {
            sh 'docker logout'
        }
    }
}