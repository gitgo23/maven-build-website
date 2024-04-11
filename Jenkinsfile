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

        stage('Deploy to Nexus') {
            steps {
                nexusArtifactUploader artifacts: [[artifactId: 'earth-app', 
                classifier: '', 
                file: 'target/earth-app-1.0-SNAPSHOT.war', 
                type: 'war']], 
                credentialsId: 'NEXUS_CRED', 
                groupId: 'com.devops.maven', 
                nexusUrl: '34.224.3.182:8081', 
                nexusVersion: 'nexus3', 
                protocol: 'http', 
                repository: 'EarthApp-Snapshot', 
                version: '1.0-SNAPSHOT'
            }
        }

        stage('TOMCAT') {
            steps {
                deploy adapters: [tomcat9(credentialsId: 'TOMCAT_CRED', 
                path: '', 
                url: 'http://100.26.195.246:8080/')], 
                contextPath: null, 
                war: 'target/*.war'
            }
        }
    }
}