pipeline {
    agent any

    environment {
        DOCKERHUB_USERNAME = "veenay910"
        DOCKERHUB_CREDENTIALS = "docker"
        IMAGE_TAG = "${BUILD_NUMBER}"
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
                    env.BUILD_CATALOGUE = "false"

                    if (changes.contains("debug/")) {
                        env.BUILD_DEBUG = "true"
                    }

                    if (changes.contains("mongodb/")) {
                        env.BUILD_MONGODB = "true"
                    }

                    if (changes.contains("catalogue/")) {
                        env.BUILD_CATALOGUE = "true"
                    }

                    echo "BUILD_DEBUG: ${env.BUILD_DEBUG}"
                    echo "BUILD_MONGODB: ${env.BUILD_MONGODB}"
                    echo "BUILD_CATALOGUE: ${env.BUILD_CATALOGUE}"
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
                sh """
                    docker build -t ${DOCKERHUB_USERNAME}/debug:${IMAGE_TAG} ./debug
                    docker push ${DOCKERHUB_USERNAME}/debug:${IMAGE_TAG}
                """
            }
        }

        stage('Build & Push MongoDB Image') {
            when {
                expression { env.BUILD_MONGODB == "true" }
            }
            steps {
                sh """
                    docker build -t ${DOCKERHUB_USERNAME}/mongodb:${IMAGE_TAG} ./mongodb
                    docker push ${DOCKERHUB_USERNAME}/mongodb:${IMAGE_TAG}
                """
            }
        }

        stage('Build & Push Catalogue Image') {
            when {
                expression { env.BUILD_CATALOGUE == "true" }
            }
            steps {
                sh """
                    docker build -t ${DOCKERHUB_USERNAME}/catalogue:${IMAGE_TAG} ./catalogue
                    docker push ${DOCKERHUB_USERNAME}/catalogue:${IMAGE_TAG}
                """
            }
        }
    }

    post {
        always {
            sh 'docker logout'
        }
    }
}