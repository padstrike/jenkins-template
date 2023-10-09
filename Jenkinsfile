pipeline {
    
    agent any 

    stages {
        stage('Checkout') {
            steps {
                // Get the code from the version control system
                checkout scm 
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
