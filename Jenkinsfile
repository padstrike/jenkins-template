pipeline {
    agent any

    environment {
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

                    // Push to Docker Hub
                    sh '''
                        echo "Building Image..."
                        docker build -t $imageName:$imageTag .
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
