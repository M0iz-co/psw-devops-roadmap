pipeline {

    agent any

    environment {
        IMAGE_NAME = "psw-nginx"
        IMAGE_TAG = "${BUILD_NUMBER}"
        HARBOR = "localhost:8088"
    }

    stages {

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh """
                docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .
                """
            }
        }

        stage('Login to Harbor') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'harbor-creds',
                    usernameVariable: 'HARBOR_USER',
                    passwordVariable: 'HARBOR_PASS'
                )]) {

                    sh """
                    echo \$HARBOR_PASS | docker login ${HARBOR} -u \$HARBOR_USER --password-stdin
                    """
                }
            }
        }

        stage('Tag Image') {
            steps {
                sh """
                docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${HARBOR}/library/${IMAGE_NAME}:${IMAGE_TAG}
                """
            }
        }

        stage('Push Image') {
            steps {
                sh """
                docker push ${HARBOR}/library/${IMAGE_NAME}:${IMAGE_TAG}
                """
            }
        }

    }
}
