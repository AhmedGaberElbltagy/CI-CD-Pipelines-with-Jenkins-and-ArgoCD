pipeline {
    agent any

    tools {
        gradle 'gradle' // Use the exact name configured in Jenkins for Gradle
    }

    stages {
        stage('Cleanup Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Test') {
            steps {
                script {
                    sh './gradlew test'
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    sh './gradlew build'
                }
            }
        }

        stage('Package') {
            steps {
                script {
                    sh './gradlew bootJar'
                }
            }
        }

        stage('Build and Push Image') {
            steps {
                script {
                    buildImage 'gitOps'
                    dockerLogin()
                    pushImage 'gitOps'
                }
            }
        }
    }
}
