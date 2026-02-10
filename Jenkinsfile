pipeline {
     agent {
        docker {
            image 'docker:29.0-cli'
            //args '--privileged -v /var/run/docker.sock:/var/run/docker.sock --user root'
        }
    }

    environment {
        DOCKERHUB_USER = "mandperfect"
        DOCKER_PATH = "weather-app-python"
        
    }

    stages {

        stage('Checkout Code from SCM') {
            steps {
                git branch: 'main',
                url: 'https://github.com/mandperfect/weather-app-python.git'
            }
        }

        stage('Build Docker Images') {
            steps {
                script {
                    services = ["alert-service", "gateway", "weather-service"]

                    for (service in services) {
                        sh """
                        docker build -t ${DOCKERHUB_USER}/${service}:latest ${DOCKER_PATH}/${service}
                        """
                    }
                }
            }
        }

        stage('Push Docker Images') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-cred',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'PASS'
                )]) {
                    sh """
                    echo $PASS | docker login -u $DOCKER_USER --password-stdin
                    """

                    script {
                        services = ["alert-service", "gateway", "weather-service"]

                        for (service in services) {
                            sh """
                            docker push ${DOCKERHUB_USER}/${service}:latest
                            """
                        }
                    }
                }
            }
        }
    }

    post {
        success {
            echo "All images built and pushed successfully"
        }
        failure {
            echo "Build Pipeline failed. Please check the logs for details."
        }
    }
}
