
pipeline {
    agent any

    environment {
        IMAGE_NAME = 'jenkins-flask-lab'
        CONTAINER_NAME = 'jenkins-flask-app'
        APP_PORT = '8081'
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Source code is available in the workspace.'
            }
        }

        stage('Docker Check') {
            steps {
                sh 'whoami'
                sh 'docker --version'
                sh 'docker ps'
            }
        }

        stage('Build') {
            steps {
                sh 'docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} .'
            }
        }

        stage('Test') {
            steps {
                sh 'docker run --rm ${IMAGE_NAME}:${BUILD_NUMBER} python -m pytest -v'
    }
}

        stage('Deploy') {
            steps {
                sh '''
                    docker rm -f ${CONTAINER_NAME} || true

                    docker run -d \
                        --name ${CONTAINER_NAME} \
                        -p ${APP_PORT}:8000 \
                        -e APP_VERSION=${BUILD_NUMBER} \
                        ${IMAGE_NAME}:${BUILD_NUMBER}
                '''
            }
        }
        stage('Debug Network') {
            steps {
                sh 'whoami'
                sh 'hostname'
                sh 'docker ps'
                sh 'curl -4 -v http://127.0.0.1:8081/health'
        }
    }
        stage('Verify') {
            steps {
            sh 'curl -4 --fail --retry 10 --retry-delay 2 http://127.0.0.1:${APP_PORT}/health'
            sh 'curl -4 --fail http://127.0.0.1:${APP_PORT}/'
    }
}
    }

    post {
        success {
            echo 'Pipeline completed successfully.'
        }

        failure {
            echo 'Pipeline failed. Check the stage that reported an error.'
        }
    }
}
