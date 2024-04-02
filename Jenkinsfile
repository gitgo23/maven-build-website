pipeline {
    agent any

    tools {
        maven "Maven"
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

        stage("SonarQube Analysis") {
            steps {
              withSonarQubeEnv('sonarqube') {
                sh 'mvn sonar:sonar'
              }
            }
        }
    }
}