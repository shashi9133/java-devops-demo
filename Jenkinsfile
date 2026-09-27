pipeline {
	
	agent any
	
	stages {
		
		stage('Checkout') {
			steps {
				checkout scm
			}
		}
	}
	
	stage('Build') {
		steps {
			sh 'mvn clean package -DskipTests'
		}
	}
	
	stage('Deploy') {
		steps {
			sh 'sleep 5'
			sh 'curl -f http://localhost:8080/hello'
		}
	}
}

post {
	success {
		echo '=== CI/CD Pipeline Completed Successfully ==='
	}
	
	failure {
		echo '=== CI/CD Pipeline Failed ==='
	}
}