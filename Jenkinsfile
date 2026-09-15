pipeline {
    agent any

    parameters {
        string(
            name: 'IMAGE',
            defaultValue: 'shrutikashikar/d6-2-app',
            description: 'Docker image to scan'
        )
    }

    environment {
        DOCKER_IMAGE = 'shrutikashikar/d6-2-app'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'echo "Building application..."'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE:$BUILD_NUMBER .'
            }
        }

        stage('Security Scan') {
            steps {
                sh '''
                    echo "=== Running Trivy vulnerability scan ==="
                    echo "Scanning image: $IMAGE"

                    trivy image \
                        --severity CRITICAL \
                        --exit-code 1 \
                        --no-progress \
                        "$IMAGE"
                '''
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-creds',
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {
                    sh '''
                        echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin
                        docker push "$DOCKER_IMAGE:$BUILD_NUMBER"
                        docker logout
                    '''
                }
            }
        }
    }
}
