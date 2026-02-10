pipeline {
    agent any

    environment {
            DOCKERHUB_USER = "mandperfect"
            IMAGE_NAME = "weather-app-build"
            IMAGE_TAG = "2.0"
            SERVICES = "gateway:4000 weather-service:5000 alert-service:6000"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/mandperfect/weather-app-python.git'
            }
        }

        stage('Build Docker Images (Using Loop)') {
            steps {
                sh '''
                for service in $SERVICES; do
                    name=$(echo $service | cut -d':' -f1)
                    port=$(echo $service | cut -d':' -f2)

                    echo "-----------------------------------------"
                    echo "Building Docker Image for: $name"
                    echo "Expected Port: $port"
                    echo "-----------------------------------------"

                    docker build -t $DOCKERHUB_USER:IMAGE_NAME ./$IMAGE_TAG .
                    // docker build -t $DOCKERHUB_USER/$IMAGE_NAME:$IMAGE_TAG .
                done
                '''
            }
        }

        stage('List Built Images (Reliable Check)') {
            steps {
                sh '''
                echo "========== Docker Images Built by Jenkins =========="
                docker images | grep -E "gateway|weather-service|alert-service" || true
                echo "===================================================="
                '''
            }
        }

        stage('Verify Images') {
            steps {
                sh '''
                docker images | grep -E "gateway|weather-service|alert-service" || true
                '''
            }
        }
    }

    post {
        success {
            echo "All three Docker images built successfully!"
        }
        failure {
            echo "Build failed. Check logs."
        }
    }
}
