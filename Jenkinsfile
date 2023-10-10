def notify_LINE(status, token) {
    // def jobname = env.JOB_NAME
    // def buildno = env.BUILD_NUMBER
    def timestamp = new Date().format("yyyy-MM-dd : ['T'HH:mm:ss.SSSXXX]")
    // def joburl = env.JOB_URL
    
    def url = 'https://notify-api.line.me/api/notify'
    def message = "Hi ${timestamp}"
    sh "curl ${url} -H 'Authorization: Bearer ${token}' -F 'message= \n ${message}'"
}


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
                    dir('env-template') {
                        sh 'cp .env ../'  // Copy .env file to the first repository's directory
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

        stage('Deploy') {
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

    post {
    success {
      notify_LINE("Success","qF5XKt7ffKCmqLyAs5Pk7M1fPRhSgqivj4Aji2D0mS6")
    }
    failure {
     
      notify_LINE("Failed" ,"qF5XKt7ffKCmqLyAs5Pk7M1fPRhSgqivj4Aji2D0mS6")
    }
   
  } // End of LINE notificaion

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
