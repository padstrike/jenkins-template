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

        stage('Install') {
            steps {
                // Install dependencies
                echo "npm install"
            }
        }

        stage('Test') {
            steps {
                // Run tests
                echo "npm test"
            }
        }

        stage('Build') {
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
            // credentialsId: 'git-credentials-id',  // Uncomment and replace with the actual credentials ID if needed
            url: repoUrl
        ]]
    ])
}