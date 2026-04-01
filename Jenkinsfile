pipeline {
    agent any

    parameters {
        choice(name: 'APP_TYPE', choices: ['mongodb', 'debug'], description: 'Select application type')
    }

    environment {
        DOCKERHUB_USERNAME = "veenay910"
        CONTAINER_NAME = "app-container"
    }

    stages {

        stage('Clean Workspace') {
            steps {
                deleteDir()
            }
        }

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Set Variables') {
            steps {
                script {
                    if (params.APP_TYPE == 'mongodb') {
                        env.IMAGE_NAME = "mongo-init-image"
                        env.DOCKERFILE_PATH = "mongodb/Dockerfile"
                        env.BUILD_CONTEXT = "mongodb"
                    } else {
                        env.IMAGE_NAME = "debug-image"
                        env.DOCKERFILE_PATH = "debug/Dockerfile"
                        env.BUILD_CONTEXT = "debug"
                    }

                    env.FULL_IMAGE_NAME = "${DOCKERHUB_USERNAME}/${IMAGE_NAME}"

                    echo "APP_TYPE: ${params.APP_TYPE}"
                    echo "IMAGE_NAME: ${IMAGE_NAME}"
                    echo "DOCKERFILE: ${DOCKERFILE_PATH}"
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh """
                docker build --no-cache \
                -t ${FULL_IMAGE_NAME}:${BUILD_NUMBER} \
                -f ${DOCKERFILE_PATH} \
                ${BUILD_CONTEXT}
                """
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'docker',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS'
                )]) {
                    sh "echo $PASS | docker login -u $USER --password-stdin"
                }
            }
        }

        stage('Push Image') {
            steps {
                sh """
                docker push ${FULL_IMAGE_NAME}:${BUILD_NUMBER}
                """
            }
        }

    }
}