pipeline {
    agent any

    environment {
        DOCKERHUB_USER = "mandperfect"
        IMAGE_TAG = "latest"
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

                    docker build -t ${name}:${IMAGE_TAG} ./${name}
                done
                '''
            }
        }

        stage('List Built Images') {
            steps {
                sh '''
                echo "========== Docker Images Built =========="
                docker images | grep -E "gateway|weather-service|alert-service" || true
                echo "========================================="
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
