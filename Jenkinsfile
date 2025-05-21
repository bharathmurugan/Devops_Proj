pipeline {
    agent any

    environment {
        IMAGE_NAME = "bharathm746/jenkins-k8s-app:latest"
        KUBECONFIG_CREDENTIALS = credentials('kubeconfig-credentials-id')
    }

    stages {
        stage('Clone Code') {
            steps {
                git 'https://github.com/bharathmurugan/jenkins-k8s-deployment.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-id', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh """
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push $IMAGE_NAME
                    """
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                withCredentials([file(credentialsId: 'kubeconfig-credentials-id', variable: 'KUBECONFIG')]) {
                    sh """
                        export KUBECONFIG=$KUBECONFIG
                        kubectl apply -f k8s/deployment.yaml
                        kubectl apply -f k8s/service.yaml
                    """
                }
            }
        }
    }
}
