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

        stage("Quality Gate") {
            steps {
              timeout(time: 1, unit: 'HOURS') {
                waitForQualityGate abortPipeline: true
              }
            }
        }

        stage('Deploy to Nexus') {
            steps {
                nexusArtifactUploader artifacts: [[artifactId: 'earth-app', 
                classifier: '', 
                file: '/var/lib/jenkins/workspace/Earth_App_Project/target/earth-app-1.0-SNAPSHOT.war', 
                type: 'war']], 
                credentialsId: 'NEXUS_CRED', 
                groupId: 'com.devops.maven', 
                nexusUrl: '54.209.142.137:8081', 
                nexusVersion: 'nexus3', 
                protocol: 'http', 
                repository: '54.209.142.137:8081', 
                version: '1.0-SNAPSHOT'
            }
        }
    }
}