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
                    //  sh 'chmod +x ./gradlew'
                    // sh './gradlew build'
                    echo 'building'
                }
            }
        }

        stage('Package') {
            steps {
                script {
                    // sh 'chmod +x ./gradlew'
                    // sh './gradlew bootJar'
                    echo 'packageing'
                }
            }
        }

        stage('Build and Push Image') {
            steps {
                script {
                    env.RELEASE_VERSION = sh(
                        script: "git log -1 --pretty=format:%H | head -c 9", returnStdout: true ).trim()
                    
                    echo "RELEASE_VERSION ${env.RELEASE_VERSION} "

                    // buildImage "gitops:${env.RELEASE_VERSION}"
                    // dockerLogin()
                    // pushImage "gitops:${env.RELEASE_VERSION}"
                }
            }
        }
        stage('Promote to Dev Environment') {
            steps {
                script {
                        withCredentials([string(credentialsId: 'Token', variable: 'GITHUB_TOKEN')]) {
                        sh """
                        git config --global user.email ahmedelbltagy1999@gmail.com && git config --global user.name ahmedgaberelbltagy
                        git clone https://\${GITHUB_TOKEN}@github.com/AhmedGaberElbltagy/manifest-files.git
                        cd manifest-files
                        echo "checkout main branch"
                        git checkout main
                        sed -i 's|image: .*|image: ahmedelbltagy/gitops:${env.RELEASE_VERSION}|' deployment.yaml
                        echo "updating image tag in values file"
                        git add . && git commit -m "update image tag"
                        git push https://\${GITHUB_TOKEN}@github.com/AhmedGaberElbltagy/manifest-files.git
                        """
                        }     
                }
            }
        }
    }
}


        

        
        
