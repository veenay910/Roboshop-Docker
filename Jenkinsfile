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

                    echo "Selected: ${IMAGE_NAME}"
                }
            }
        }

        stage('Build') {
            steps {
                sh """
                docker build --no-cache -t ${FULL_IMAGE_NAME}:${BUILD_NUMBER} -f ${DOCKERFILE_PATH} ${BUILD_CONTEXT}
                """
            }
        }

    }
}