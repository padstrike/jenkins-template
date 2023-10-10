pipeline {
  agent any
  stages {
    stage('Checkout') {
      steps {
        checkout scm
        git(url: 'https://github.com/padstrike/web-react.git', branch: 'dev', changelog: true)
      }
    }

    stage('Install') {
      steps {
        echo 'npm install'
      }
    }

    stage('Test') {
      steps {
        echo 'npm test'
      }
    }

    stage('Build') {
      steps {
        echo 'npm run build'
      }
    }

  }
}