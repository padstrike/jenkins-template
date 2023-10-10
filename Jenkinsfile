pipeline {
    agent any

    environment {
        DOCKER_CREDENTIALS = credentials('admin') 
        imageName = 'production'
        imageTag = 'version'
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
                    // Build Docker image
                    sh "docker build -t ${imageName}:${imageTag} ."

                    // List all local Docker images
                    sh "docker images"

                    // Push to Docker Hub
                    sh '''
                        echo "Tagging Image..."
                        docker tag $imageName:$imageTag $DOCKER_CREDENTIALS_USR/$imageName:$imageTag
                        echo "Logging in to Docker Hub..."
                        echo "$DOCKER_CREDENTIALS_PSW" | docker login -u $DOCKER_CREDENTIALS_USR --password-stdin
                        echo "Pushing Image..."
                        docker push $DOCKER_CREDENTIALS_USR/$imageName:$imageTag
                    '''
                }
            }
        }

        stage('Update Application Container') {
            steps {
                script {
                    sh '''
                        echo "Pulling Image..."
                        docker pull $DOCKER_CREDENTIALS_USR/$imageName:$imageTag
                        echo "Stopping Existing Container..."
                        docker stop jenkins-test-container || true
                        echo "Removing Existing Container..."
                        docker rm jenkins-test-container || true
                        echo "Running New Container..."
                        docker run -d -p 3000:3000 --name jenkins-test-container $DOCKER_CREDENTIALS_USR/$imageName:$imageTag
                    '''
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
