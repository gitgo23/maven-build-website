pipeline {
    agent any

    tools {
        maven 'Maven-3.9.6'
    }

    stages {
        stage('Git Checkout') {
            steps {
                git 'https://github.com/gyenoch/maven-build-website.git'
            }
        }

        stage('Build with Maven') {
            steps {
                sh '''
                "echo Starting Clean Packaging"
                "mvn clean package"
                "echo Clean Packaging Success"
                '''
            }
        }
    }
}