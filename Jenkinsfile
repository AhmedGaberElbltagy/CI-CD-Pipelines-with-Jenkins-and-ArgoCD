@Library('Jenkins-Shared_library')
def gv
pipeline {
    agent any
    environment{
        // AWS_ACCESS_KEY_ID = credentials('jenkins-aws-access-key-id')
        // AWS_SECRET_ACCESS_KEY = credentials('jenkins-aws-secret-access-key-id')
        // KUBE_NAMESPACE = 'development'		
        // INGRESS_NAMESPACE = 'ingress-nginx'	
    }
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
                    testApp()
                }
            }
        }
        
        stage('Build') {
            steps {
                script {
                    //buildJar functions is avaliable in the jenkins-shared-library
                    buildJar()
                }
            }
        }
        stage('Package') {
            steps {
                script {
                    //packageApp functions is avaliable in the jenkins-shared-library
                    packageApp()
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
       