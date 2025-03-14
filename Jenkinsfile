pipeline {
    agent any

    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/Jithendra-Jithu/example-voting-app.git'
            }
        }

        stage('Build with Maven') {
            steps {
                sh '''
                mvn clean install
                '''
            }
        }

        stage('Build Docker Images') {
            steps {
                sh '''
                docker build -t vote-app:latest ./vote
                docker build -t result-app:latest ./result
                docker build -t worker-app:latest ./worker
                '''
            }
        }

        stage('Deploy to Minikube') {
            steps {
                sh '''
                kubectl apply -f ./k8s-specifications/db-deployment.yaml
                kubectl apply -f ./k8s-specifications/db-service.yaml
                kubectl apply -f ./k8s-specifications/redis-deployment.yaml
                kubectl apply -f ./k8s-specifications/redis-service.yaml
                kubectl apply -f ./k8s-specifications/result-deployment.yaml
                kubectl apply -f ./k8s-specifications/result-service.yaml
                kubectl apply -f ./k8s-specifications/vote-deployment.yaml
                kubectl apply -f ./k8s-specifications/vote-service.yaml
                kubectl apply -f ./k8s-specifications/worker-deployment.yaml

                # Check rollout status for all deployments
                kubectl rollout status deployment/db-deployment
                kubectl rollout status deployment/redis-deployment
                kubectl rollout status deployment/result-deployment
                kubectl rollout status deployment/vote-deployment
                kubectl rollout status deployment/worker-deployment
                '''
            }
        }
    }

    post {
        success {
            echo "Deployment completed successfully."
        }
        failure {
            echo "Deployment failed. Check logs for details."
        }
    }
}
