pipeline {

    agent any

    environment {
        DOCKER_IMAGE = 'shashinani/java-devops-demo'
    }

    stages {

        stage('Build') {
            steps {
                echo '=== Building Spring Boot application ==='

                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Docker Build') {
            steps {
                echo '=== Building Docker image ==='

                sh """
                    docker build \
                        -t ${DOCKER_IMAGE}:build-${BUILD_NUMBER} \
                        -t ${DOCKER_IMAGE}:latest .
                """
            }
        }

        stage('Docker Push') {
            steps {
                echo '=== Pushing Docker image to Docker Hub ==='

                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockeerhub-credentials',
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {

                    sh '''
                        echo "$DOCKER_PASSWORD" | docker login \
                            -u "$DOCKER_USERNAME" \
                            --password-stdin

                        docker push ${DOCKER_IMAGE}:build-${BUILD_NUMBER}
                        docker push ${DOCKER_IMAGE}:latest

                        docker logout
                    '''
                }
            }
        }

        stage('Docker Deploy') {
            steps {
                echo '=== Pulling image from Docker Hub and deploying ==='

                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockeerhub-credentials',
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {

                    sh '''
                        echo "$DOCKER_PASSWORD" | docker login \
                            -u "$DOCKER_USERNAME" \
                            --password-stdin

                        echo "=== Pulling image from Docker Hub ==="

                        docker pull ${DOCKER_IMAGE}:build-${BUILD_NUMBER}

                        echo "=== Removing old container ==="

                        docker rm -f java-devops-container 2>/dev/null || true

                        echo "=== Starting new container ==="

                        docker run -d \
                            --name java-devops-container \
                            -p 8080:8080 \
                            ${DOCKER_IMAGE}:build-${BUILD_NUMBER}

                        docker logout

                        sleep 10
                    '''
                }
            }
        }

        stage('Health Check') {
            steps {
                echo '=== Health Check ==='

                sh 'docker ps'

                sh 'curl -f http://localhost:8080/hello'
            }
        }
    }

    post {
        success {
            echo '=== CI/CD Pipeline is Completed Successfully ==='
        }

        failure {
            echo '=== CI/CD Pipeline FAILED ==='
        }
    }
}
