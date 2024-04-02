def COLOUR_MAP = [
    'FAILURE': 'danger',
    'SUCCESS': 'good'
]

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
                        file: 'target/earth-app-1.0-SNAPSHOT.war',
                        type: 'war']],
                        credentialsId: 'NEXUS_CRED',
                        groupId: 'com.devops.maven',
                        nexusUrl: '54.209.142.137:8081',
                        nexusVersion: 'nexus3',
                        protocol: 'http',
                        repository: 'EarthApp-Snapshot',
                        version: '1.0-SNAPSHOT'
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                deploy adapters: [tomcat9(credentialsId: 'TOMCAT',
                        path: '',
                        url: 'http://184.72.139.19:8080/')],
                        contextPath: null,
                        war: 'target/*.war'
            }
        }
    }

    post {
        always {
            script {
                echo 'Slack Notifications'
                def color = COLOUR_MAP[currentBuild.currentResult.toString()]
                slackSend (
                    channel: 'jenkins-slack-notification',
                    color: color ?: 'warning', // Default to 'warning' if color not found
                    message: "${currentBuild.currentResult}: Job ${env.JOB_NAME} \n build ${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URL}"
                )
            }
        }
    }
}
