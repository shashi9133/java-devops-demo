pipeline {

    agent any

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
                sh 'docker build -t java-devops-demo:latest .'
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
                echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin

                docker tag java-devops-demo:latest $DOCKER_USERNAME/java-devops-demo:build-${BUILD_NUMBER}

                docker push $DOCKER_USERNAME/java-devops-demo:build-${BUILD_NUMBER}
                
                docker tag java-devops-demo:latest $DOCKER_USERNAME/java-devops-demo:latest
                
                docker push $DOCKER_USERNAME/java-devops-demo:latest

                docker logout
            '''
        }
    }
}

        stage('Docker Deploy') {
            steps {
                echo '=== Deploying Docker container ==='

                sh '''
                    docker stop java-devops-container || true
                    docker rm java-devops-container || true

                    docker run -d \
                        --name java-devops-container \
                        -p 8080:8080 \
                        java-devops-demo:latest
                '''

                sleep 10
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
            echo '=== CI/CD Pipeline Completed Successfully ==='
        }

        failure {
            echo '=== CI/CD Pipeline FAILED ==='
        }
    }
}