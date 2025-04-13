pipeline {
    agent any

    environment {
        IMAGE_NAME = "anastefanovska/kiiii-jenkins"
    }

    stages {
        stage('Clone repository') {
            steps {
                checkout scm
            }
        }

        stage('Build image') {
            steps {
                script {
                    echo "Building Docker image: ${IMAGE_NAME}"
                    docker.build("${IMAGE_NAME}")
                }
            }
        }

        stage('Push image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                        def image = docker.image("${IMAGE_NAME}")

                        echo "Pushing tag: ${env.BRANCH_NAME}-${env.BUILD_NUMBER}"
                        def pushStatus1 = image.push("${env.BRANCH_NAME}-${env.BUILD_NUMBER}", true)

                        echo "Pushing tag: ${env.BRANCH_NAME}-latest"
                        def pushStatus2 = image.push("${env.BRANCH_NAME}-latest", true)

                        echo "Push complete."
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Build succeeded!'
            githubNotify context: 'CI Pipeline', status: 'SUCCESS', description: 'Build and push successful!'
        }
        failure {
            echo 'Build failed.'
            githubNotify context: 'CI Pipeline', status: 'FAILURE', description: 'Something went wrong in the pipeline.'
        }
    }
}
