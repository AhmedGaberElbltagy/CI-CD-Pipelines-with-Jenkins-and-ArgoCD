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
         stage('Lint') {
            steps {
                script {
                  // Run the gradle check task, which includes linting
                    //LintApp functions is avaliable in the jenkins-shared-library
                    
                    lintApp()
                }
            }
            post {
                always {
                    // Archive the linting report if any
                    archiveArtifacts artifacts: '**/build/reports/**', allowEmptyArchive: true
                }
            }
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
                     //testApp functions is avaliable in the jenkins-shared-library
                    sh './gradlew test'
                }
            }
        }
        stage('Build') {
            steps {
                script {
                     //BootJar functions is avaliable in the jenkins-shared-library
                    // sh './gradlew build'
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

        stage('Build and Push Image') {
            steps {
                script {
                     //buildImage , pushImage ,docker login functions is avaliable in the jenkins-shared-library,
                     // and they takes a varaiable 'image name'
                     // the image Tag is equal to the commit Hash
                    env.RELEASE_VERSION = sh(
                        script: "git log -1 --pretty=format:%H | head -c 9", returnStdout: true ).trim()
                    
                    echo "RELEASE_VERSION ${env.RELEASE_VERSION} "
                    buildImage "gitops:${env.RELEASE_VERSION}"
                    dockerLogin()
                    pushImage "gitops:${env.RELEASE_VERSION}"
                }
            }
        }
        stage('Promote to Dev Environment') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'Token', variable: 'GITHUB_TOKEN'),
                                    string(credentialsId: 'email', variable: 'EMAIL'),
                                    string(credentialsId: 'username', variable: 'USERNAME')]) {
                        sh """
                        git config --global user.email ${EMAIL} && git config --global user.name ${USERNAME}
                        git clone https://\${GITHUB_TOKEN}@github.com/${USERNAME}/manifest-files.git
                        cd manifest-files
                        echo "checkout main branch"
                        git checkout main
                        sed -i 's|image: .*|image: ahmedelbltagy/gitops:${env.RELEASE_VERSION}|' deployment.yaml
                        echo "updating image tag in values file"
                        git add . && git commit -m "update image tag"
                        git push https://\${GITHUB_TOKEN}@github.com/${USERNAME}/manifest-files.git
                        """
                    }
                }
            }
        }
    }
    }
}


        

        
        
