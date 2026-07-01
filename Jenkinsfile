pipeline {
    agent any
    tools {
        maven 'Maven3'
    }
    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-creds')
        IMAGE_NAME = "bnuthana/my-firstapp"
        IMAGE_TAG = "${BUILD_NUMBER}"
    }
    
    stages {
        stage('Build JAR') {
            steps {
                bat 'mvn -Dmaven.test.failure.ignore=true clean package'
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    echo "Building: ${IMAGE_NAME}:${IMAGE_TAG}"
                    docker.build("${IMAGE_NAME}:${IMAGE_TAG}")
                }
            }
        }
        
        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-creds') {
                        docker.image("${IMAGE_NAME}:${IMAGE_TAG}").push()
                        docker.image("${IMAGE_NAME}:${IMAGE_TAG}").push("latest")
                    }
                }
            }
        }
        
        stage('Deploy to K8s') {
            steps {
                withEnv(["KUBECONFIG=C:\\Users\\bnuth\\.kube_jenkins\\config"]) {
                    bat 'kubectl apply -f k8s-deployment.yaml'
                    bat "kubectl set image deployment/myfirstapp-deployment myfirstapp=${IMAGE_NAME}:${IMAGE_TAG}"
                    bat 'kubectl rollout status deployment/myfirstapp-deployment --timeout=120s'
                }
            }
        }
    
    post {
        success {
            echo "SUCCESS: Deployed ${IMAGE_NAME}:${IMAGE_TAG}"
            echo "Run: kubectl get svc myfirstapp-service"
        }
    }
}
