pipeline {

    agent any

    stages {

        stage('Checkout') {
            steps {
				echo '=== Checking out source code ==='
                checkout scm
            }
        }

        stage('Build') {
            steps {
				echo '=== Building Spring Boot application ==='
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Deploy') {
            steps {
				echo '=== Deploying application ==='
                sh 'sudo systemctl restart java-devops-demo'
                sleep 5
            }
        }

        stage('Health Check') {
            steps {
                echo '=== Health Check ==='
                sh 'sudo systemctl is-active java-devops-demo'
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