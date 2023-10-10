pipeline {
    
    agent any 

    stages {
        stage('Checkout') {
            steps {
                script {
                    checkoutRepo('https://github.com/padstrike/web-react.git', 'main')
                    dir('env-repo') {
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
                // Build the project
                echo "npm run build"
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