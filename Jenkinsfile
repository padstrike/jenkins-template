pipeline {
    agent any

    environment {
        DOCKER_CREDENTIALS = credentials('admin')
    }

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

                    script {
                    // Build Docker image
                    def imageName = 'production'
                    def imageTag = 'vertion'
                    sh "docker build -t ${imageName}:${imageTag} ."

                    // Push to Docker Hub

                    sh "docker login -u $DOCKER_HUB_USERNAME --password-stdin"
                    sh "docker push $DOCKER_CREDENTIALS_USR/${imageName}:${imageTag}"
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
            // credentialsId: 'admin',  // Uncomment and replace with the actual credentials ID if needed
            url: repoUrl
        ]]
    ])
}
