pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'hello-world-python:latest'             // Local image name
        DOCKERHUB_REPO = 'vrushabhvirkar/hello-world-python'   // Replace with your DockerHub repo
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Vrushabhvirkar/jenkins.git'
            }
        }

        stage('Docker Build') {
            steps {
                script {
                    if (fileExists('Dockerfile')) {
                        sh "docker build -t ${DOCKER_IMAGE} ."
                    } else {
                        error "Dockerfile not found in the workspace."
                    }
                }
            }
        }

        stage('Tag Image for DockerHub') {
            steps {
                sh "docker tag ${DOCKER_IMAGE} ${DOCKERHUB_REPO}:latest"
            }
        }

        stage('DockerHub Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',   // Your Jenkins credential ID
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS'
                )]) {
                    sh 'echo "$PASS" | docker login -u "$USER" --password-stdin'
                }
            }
        }

        stage('Push to DockerHub') {
            steps {
                sh "docker push ${DOCKERHUB_REPO}:latest"
            }
        }

        stage('Docker Run (Optional)') {
            steps {
                sh "docker run --rm ${DOCKERHUB_REPO}:latest"
            }
        }
    }

    post {
        success {
            echo 'Docker image successfully built and pushed to DockerHub!'
        }
        failure {
            echo 'Pipeline failed — check logs.'
        }
    }
}
