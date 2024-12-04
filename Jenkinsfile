@Library('Jenkins-Shared_library')
def gv
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

        // stage('Test') {
        //     steps {
        //         script {
        //             sh 'chmod +x ./gradlew'
        //             sh './gradlew test'
        //         }
        //     }
        // }

        stage('Build') {
            steps {
                script {
                     sh 'chmod +x ./gradlew'
                    sh './gradlew build'
                    echo 'building'
                }
            }
        }

        stage('Package') {
            steps {
                script {
                    sh 'chmod +x ./gradlew'
                    sh './gradlew bootJar'
                    echo 'packageing'
                }
            }
        }

        stage('Build and Push Image') {
            steps {
                script {
                    env.RELEASE_VERSION = sh(
                        script: "git log -1 --pretty=format:%H | head -c 9", returnStdout: true ).trim()
                    
                    echo 'RELEASE_VERSION is ${env.RELEASE_VERSION}'

                    buildImage "gitops:${env.RELEASE_VERSION}"
                    dockerLogin()
                    pushImage "gitops:${env.RELEASE_VERSION}"
                }
            }
        }
    }
}


       stage('Promote to Dev Environment') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'github_credientials', usernameVariable: 'GIT_USER', passwordVariable: 'GIT_PASS')]) {
                        sh """
                        git clone https://${GIT_USER}:${GIT_PASS}@github.com/<GIT_ORG>/<REPO_NAME>.git
                        pwd && ls
                        """
                    }
                }
            }
        }

        
        
