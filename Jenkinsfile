
pipeline {
    agent any

    environment {
        IMAGE_NAME = 'ashokraji/retail-ui'
        UI_DIR = 'src/ui'
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
                    sh 'chmod +x mvnw'
                    sh './mvnw test'
                }
            }
        }

        stage('Maven Build and Package') {
            steps {
                dir("${UI_DIR}") {
                    sh './mvnw -DskipTests package'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                dir("${UI_DIR}") {
                    sh '''
                        set -eu

                        docker build \
                          -t "$IMAGE_NAME:$BUILD_NUMBER" \
                          -t "$IMAGE_NAME:latest" \
                          .
                    '''
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-creds',
                        usernameVariable: 'DH_USER',
                        passwordVariable: 'DH_TOKEN'
                    )
                ]) {
                    sh '''
                        set +x
                        set -eu

                        echo "$DH_TOKEN" | docker login \
                          --username "$DH_USER" \
                          --password-stdin

                        docker push "$IMAGE_NAME:$BUILD_NUMBER"
                        docker push "$IMAGE_NAME:latest"

                        docker logout
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'UI image built and pushed successfully.'
        }

        failure {
            echo 'Pipeline failed. Check the failed stage in Jenkins.'
        }

        always {
            echo 'UI pipeline execution completed.'
        }
    }
}
