pipeline {
    agent any
    
    tools {
        jdk 'JDK21'
        maven 'Maven3'
    }
    
    stages {
        stage('Build') {
            steps {
                bat 'mvn -Dmaven.test.failure.ignore=true clean package'
            }
        }
        // ... rest of your stages
    }
}