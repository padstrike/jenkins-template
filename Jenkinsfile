pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                script {
                    // Check out the first repository
                    checkoutRepo('https://github.com/padstrike/web-react.git', 'main')

                    // Check out the second repository to a different directory
                    dir('env-template') {
                        checkoutRepo('https://github.com/padstrike/env-template.git', 'main')
                    }
                }
            }
        }

        stage('Copy-env') {
            steps {
                script {
                    // Check if the .env file already exists in the target directory before copying
                    if (!fileExists('.env')) {
                        dir('env-template') {
                            sh 'cp .env ../'  // Copy .env file to the first repository's directory
                        }
                    } else {
                        echo 'The .env file already exists in the target directory.'
                    }
                }
            }
        }

        stage('Docker Build and Push') {
            steps {
                script {
                    // Build Docker image
                    def imageName = 'tuer12033/production'
                    def imageTag = 'version'
                    sh "docker build -t ${imageName}:${imageTag} ."

                    // List all local Docker images
                    sh "docker images"

                    // Push to Docker Hub
                    withCredentials([usernamePassword(credentialsId: 'admin', usernameVariable: 'DOCKER_HUB_USERNAME', passwordVariable: 'DOCKER_HUB_PASSWORD')]) {
                        sh "echo $DOCKER_HUB_PASSWORD | docker login -u $DOCKER_HUB_USERNAME --password-stdin"
                        sh "docker push ${imageName}:${imageTag}"
                    }
                }
            }
        }
    }
}

// Helper method for checking out a Git repository
void checkoutRepo(String repoUrl, String branchName) {
    checkout([
        $class: 'GitSCM',
        branches: [[name: branchName]],
        doGenerateSubmoduleConfigurations: false,
        extensions: [],
        submoduleCfg: [],
        userRemoteConfigs: [[
            url: repoUrl
        ]]
    ])
}
