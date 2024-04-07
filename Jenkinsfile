COLOR_MAP = [
    'SUCCESS': 'good',
    'FAILURE': 'danger',
]

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

        stage("Quality Gate") {
            steps {
              timeout(time: 1, unit: 'HOURS') {
                waitForQualityGate abortPipeline: true
              }
            }
        }

        stage('Deploy Build Artifact') {
            steps {
                nexusArtifactUploader artifacts: [[artifactId: 'earth-app', 
                classifier: '', 
                file: 'target/earth-app-1.0-SNAPSHOT.war', 
                type: 'war']], 
                credentialsId: 'NEXUS_CRED', 
                groupId: 'com.devops.maven', 
                nexusUrl: '52.72.27.240:8081', 
                nexusVersion: 'nexus3', 
                protocol: 'http', 
                repository: 'EarthApp-Snapshot', 
                version: '1.0-SNAPSHOT'
            }
        }
    }

    post {
        always {
            echo 'Slack Notifications'
            slackSend channel: 'team5-africa', 
                      color: COLOR_MAP[currentBuild.currentResult],
                      message: "${currentBuild.currentResult}: Job ${env.JOB_NAME} \n build ${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URL}"
        }
    }
}