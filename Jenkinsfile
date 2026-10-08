pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'deepasri111/college-department-portal:latest'
        DOCKER_CREDENTIALS = 'dockerhub-credentials'
    }

    stages {

        stage('Clone Code') {
            steps {
                echo 'Cloning code from GitHub...'
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                bat "docker build -t ${DOCKER_IMAGE} ."
            }
        }

        stage('Push Image') {
            steps {
                echo 'Pushing image to Docker Hub...'

                withCredentials([
                    usernamePassword(
                        credentialsId: "${DOCKER_CREDENTIALS}",
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {
                    bat 'docker login -u "%DOCKER_USERNAME%" -p "%DOCKER_PASSWORD%"'
                    bat "docker push ${DOCKER_IMAGE}"
                }
            }
        }

        stage('Test Kubernetes') {
            steps {
                echo 'Checking Kubernetes connection...'
                bat 'kubectl config current-context'
                bat 'kubectl get nodes'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                echo 'Deploying application to Kubernetes...'
                bat 'kubectl apply -f deployment.yaml'
            }
        }

        stage('Verify Deployment') {
            steps {
                echo 'Verifying Kubernetes deployment...'
                bat 'kubectl get deployments'
                bat 'kubectl get pods'
            }
        }
    }

    post {
        success {
            echo 'CI/CD Pipeline completed successfully!'
        }

        failure {
            echo 'CI/CD Pipeline failed. Check the console output.'
        }
    }
}