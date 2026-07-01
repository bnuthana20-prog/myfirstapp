pipeline {
    agent any

    tools {
        maven 'Maven_3.9.6' 
        jdk 'JDK_21'        
    }

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-creds') // ID from Jenkins credentials
        IMAGE_NAME = "bnuthana/my-firstapp"
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build JAR') {
            steps {
                bat 'mvn -Dmaven.test.failure.ignore=true clean package'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    echo "Building: ${IMAGE_NAME}:${IMAGE_TAG}"
                    bat "docker build -t \"${IMAGE_NAME}:${IMAGE_TAG}\" ."
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'dockerhub-creds', url: 'https://index.docker.io/v1/') {
                        bat "docker tag \"${IMAGE_NAME}:${IMAGE_TAG}\" \"index.docker.io/${IMAGE_NAME}:${IMAGE_TAG}\""
                        bat "docker push \"index.docker.io/${IMAGE_NAME}:${IMAGE_TAG}\""
                        bat "docker tag \"${IMAGE_NAME}:${IMAGE_TAG}\" \"index.docker.io/${IMAGE_NAME}:latest\""
                        bat "docker push \"index.docker.io/${IMAGE_NAME}:latest\""
                    }
                }
            }
        }

        stage('Deploy to K8s') {
            steps {
                withEnv(["KUBECONFIG=C:\\Users\\bnuth\\.kube_jenkins\\config"]) {
                    bat 'kubectl apply -f k8s-deployment.yaml'
                    bat 'kubectl rollout status deployment/myfirstapp-deployment --timeout=120s'
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
