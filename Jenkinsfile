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
                    } else if (params.APP_TYPE == 'debug') {
                        env.IMAGE_NAME = "debug-image"
                        env.DOCKERFILE_PATH = "debug/Dockerfile"
                        env.BUILD_CONTEXT = "debug"
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh """
                docker build -t $IMAGE_NAME:${BUILD_NUMBER} -f $DOCKERFILE_PATH $BUILD_CONTEXT
                """
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'docker',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
                }
            }
        }

        stage('Tag Image') {
            steps {
                sh """
                docker tag $IMAGE_NAME:${BUILD_NUMBER} $DOCKERHUB_USERNAME/$IMAGE_NAME:${BUILD_NUMBER}
                """
            }
        }

        stage('Push Image') {
            steps {
                sh """
                docker push $DOCKERHUB_USERNAME/$IMAGE_NAME:${BUILD_NUMBER}
                """
            }
        }

        stage('Run Container') {
            steps {
                sh """
                docker stop $CONTAINER_NAME || true
                docker rm $CONTAINER_NAME || true
                docker run -d --name $CONTAINER_NAME $IMAGE_NAME:${BUILD_NUMBER}
                """
            }
        }
    }
}