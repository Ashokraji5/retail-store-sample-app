
pipeline {
    agent any

    tools {
        maven 'maven'
    }

    environment {
        IMAGE_NAME = 'ashokraji/retail-ui'
        UI_DIR = 'src/ui'
        DOCKER_IMAGE = "${IMAGE_NAME}:${BUILD_NUMBER}"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'master',
                    url: 'https://github.com/Ashokraji5/war-web-project.git',
                    credentialsId: 'github-credentials'
            }
        }

        stage('Unit Tests') {
            steps {
                dir("${UI_DIR}") {
                    sh 'mvn test'
                }
            }
        }

        stage('Maven Build and Package') {
            steps {
                dir("${UI_DIR}") {
                    sh 'mvn -DskipTests package'
                }
            }
        }

        stage('Docker Build') {
            steps {
                dir("${UI_DIR}") {
                    sh '''
                        set -eu
                        docker build \
                            -t "$DOCKER_IMAGE" \
                            -t "$IMAGE_NAME:latest" \
                            .
                    '''
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                withDockerRegistry([
                    credentialsId: 'dockerhub-credentials',
                    url: 'https://index.docker.io/v1/'
                ]) {
                    sh '''
                        set -eu
                        docker push "$DOCKER_IMAGE"
                        docker push "$IMAGE_NAME:latest"
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'UI Docker image built and pushed successfully.'
        }

        failure {
            echo 'Pipeline failed. Check the failed stage in Jenkins.'
        }

        always {
            echo 'UI pipeline execution completed.'
        }
    }
}
