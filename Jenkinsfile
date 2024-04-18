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
                sh "mvn clean package"
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t gyenoch/maven-build-website:latest ."
            } 
        }

        stage('Docker Push') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'Docker-cred') {
                        sh "docker push gyenoch/maven-build-website:latest"
                    }
                }
            }
        }

        stage('Docker Run') {
            steps {
                sh "docker run -d -p 9090:8080 gyenoch/maven-build-website:latest"
            }
        }
    }
}