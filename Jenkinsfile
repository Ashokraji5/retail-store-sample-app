
pipeline {
    agent any

    tools {
        maven 'maven'
    }

    environment {
        DOCKER_USERNAME = 'ashokraji'
        VERSION = "1.0.${BUILD_NUMBER}"
        DOCKER_IMAGE = "${DOCKER_USERNAME}/retail-ui:${VERSION}"
        UI_DIR = 'src/ui'
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'master',
                    url: 'https://github.com/Ashokraji5/retail-store-sample-app.git',
                    credentialsId: 'github-credentials'
            }
        }

        stage('Verify Tools') {
            steps {
                sh '''
                    git --version
                    mvn -version
                    docker --version
                '''
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
                    '''
                }
            }
        }
    }

    post {
        success {
            echo "UI image pushed successfully: ${DOCKER_IMAGE}"
        }

        failure {
            echo 'Pipeline failed. Check the failed stage in Jenkins.'
        }

        always {
            echo 'UI pipeline execution completed.'
        }
    }
}
