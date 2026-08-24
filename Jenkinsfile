pipeline {
    agent any

    tools {
        maven 'maven-3.9.16' 
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'development', url: 'https://github.com/UpparaGovindu119/maven-webapplication-project-kkfunda.git'
            }
        }

        stage('Build and Nexus Deploy') {
            steps {
                sh 'mvn clean deploy'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('Sonar') {
                    sh 'mvn sonar:sonar'
                }
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                deploy adapters: [
                    tomcat9(
                        credentialsId: 'my-tomcat',
                        path: '',
                        url: 'http://54.226.113.0:8090'
                    )
                ],
                contextPath: 'maven-web-app',
                onFailure: true,
                war: 'target/*.war'
            }
        }
    }

    post {
        success {
            slackSend(
                tokenCredentialId: 'madhu',
                channel: '#jio-dev',
                color: '#00FF00',
                message: "SUCCESSFUL: Job '${env.JOB_NAME}' [${env.BUILD_NUMBER}] (${env.BUILD_URL})"
            )
        }
        failure {
            slackSend(
                tokenCredentialId: 'madhu',
                channel: '#jio-dev',
                color: '#FF0000',
                message: "FAILED: Job '${env.JOB_NAME}' [${env.BUILD_NUMBER}] (${env.BUILD_URL})"
            )
        }
    }
}
