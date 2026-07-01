pipeline {
    agent any

    tools {
        maven 'Maven3'
        jdk 'JDK21'
    }

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-creds')
        IMAGE_NAME = "bnuthana/my-firstapp"
        IMAGE_TAG = "${BUILD_NUMBER}"
        JAVA_HOME = tool 'JDK21'
        M2_HOME = "${tool 'Maven3'}"
        PATH = "${JAVA_HOME}\\bin;${M2_HOME}\\bin;${env.PATH}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build JAR') {
            steps {
                bat 'echo JAVA_HOME=%JAVA_HOME%'
                bat 'java -version'
                bat 'mvn -v'
                bat 'mvn -Dmaven.test.failure.ignore=true clean package'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    bat "docker build -t \"${IMAGE_NAME}:${IMAGE_TAG}\" ."
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'dockerhub-creds', url: 'https://index.docker.io/v1/') {
                        bat "docker push \"${IMAGE_NAME}:${IMAGE_TAG}\""
                        bat "docker tag \"${IMAGE_NAME}:${IMAGE_TAG}\" \"${IMAGE_NAME}:latest\""
                        bat "docker push \"${IMAGE_NAME}:latest\""
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
