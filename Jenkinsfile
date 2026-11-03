pipeline {
    agent any

    environment {
        IMAGE_NAME = 'harshya123/jenkins-complete-cicd'
        IMAGE_TAG = 'v1'
    }

    stages {

        stage('Git Checkout') {
            steps {
                checkout scm
                sh 'ls -l'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t "$IMAGE_NAME:$IMAGE_TAG" .'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-credentials',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push "$IMAGE_NAME:$IMAGE_TAG"
                        docker logout
                    '''
                }
            }
        }

        stage('Deploy Container') {
            steps {
                sh '''
                    docker rm -f ci-cd-demo || true
                    docker run -d --name ci-cd-demo -p 8081:80 "$IMAGE_NAME:$IMAGE_TAG"
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                sh 'curl -f http://localhost:8081'
            }
        }
    }

    post {
        success {
            echo 'CI/CD Pipeline completed successfully!'
        }
        failure {
            echo 'CI/CD Pipeline failed!'
        }
    }
}
