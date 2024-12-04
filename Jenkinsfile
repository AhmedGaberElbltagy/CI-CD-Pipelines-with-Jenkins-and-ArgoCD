
pipeline {
    agent any
    
    tools{
        gradle '8.10'
    }
    stages {
        stage('Cleanup Workspace') {
            steps {
                cleanWs()
            }
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
                    //testApp functions is avaliable in the jenkins-shared-library
                   sh './gradlew test'
                }
            }
        }
        
        stage('Build') {
            steps {
                script {
                    //buildJar functions is avaliable in the jenkins-shared-library
                   sh './gradlew build'
                }
            }
        }
        stage('Package') {
            steps {
                script {
                    //packageApp functions is avaliable in the jenkins-shared-library
                    sh './gradlew bootJar'
                }
            }
        }
        stage('Build and Push Image '){
            steps {
                script {
                     //buildImage , pushImage functions is avaliable in the jenkins-shared-library,
                     // and they takes a varaiable 'image name'
                     buildImage 'gitOps'
                     dockerLogin()
                     pushImage 'gitOps'

                }
            }
        }
}
       