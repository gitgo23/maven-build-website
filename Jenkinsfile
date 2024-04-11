pipeline {
    agent any
    tools {
        maven "Maven-3.9.6"
    }

    stages {
        stage('Git Clone') {
            steps {
                git 'https://github.com/gitgo23/maven-build-website.git'
            }
        }

        stage('Build with Maven') {
            steps {
                sh "mvn clean package"
            }
        }

        stage('SonarQube Analysis') {
            environment {
                ScannerHome = tool 'Sonar-5'
            }
            steps {
                script {
                    withSonarQubeEnv('sonarqube') {
                        sh "${ScannerHome}/bin/sonar-scanner -Dsonar.projectKey=Earth_App"
                    }
                }
            }
        }
    }
}