pipeline {
    agent any
    
    tools {
        jdk 'JDK21'  // <-- This is the new part
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