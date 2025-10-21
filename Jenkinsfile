pipeline {
    agent any
    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-creds')
        GITHUB_TOKEN = credentials('github-token')
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Bhuvisai22/project-3.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t trend-app:latest .'
            }
        }

        stage('Push to DockerHub') {
            steps {
                sh '''
                echo "🔐 Logging into DockerHub"
                docker login -u $DOCKERHUB_CREDENTIALS_USR -p $DOCKERHUB_CREDENTIALS_PSW
                docker tag trend-app:latest $DOCKERHUB_CREDENTIALS_USR/trend-app:latest
                docker push $DOCKERHUB_CREDENTIALS_USR/trend-app:latest
                '''
            }
        }

        stage('Deploy to EKS') {
            steps {
                sh '''
                echo "⚙️ Updating kubeconfig for EKS"
                aws eks update-kubeconfig --region ap-south-1 --name my-eks-cluster

                echo "🚀 Deploying to Kubernetes"
                kubectl apply -f k8s/deployment.yaml
                kubectl apply -f k8s/service.yaml
                '''
            }
        }
    }
    post {
        success {
            echo '✅ Deployment successful!'
        }
        failure {
            echo '❌ Deployment failed. Check logs.'
        }
    }
}
