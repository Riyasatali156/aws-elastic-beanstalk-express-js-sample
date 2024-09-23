pipeline {
    agent { docker { image 'node:16' } }
    environment {
        SNYK_TOKEN = credentials('SNYK_TOKEN')  // Using Jenkins credentials
    }
    stages {
        stage('Install dependencies') {
            steps {
                sh 'node --version'
                sh 'npm --version'
                sh 'npm ci'  // Ensure a clean install
                sh 'npm install express@4.20.0'
            }
        }
        stage('Snyk Scan') {
            steps {
                echo 'Scanning...'
                sh '''
                npm install snyk --save-dev
                ./node_modules/.bin/snyk auth $SNYK_TOKEN
                ./node_modules/.bin/snyk test --severity-threshold=high
                '''
            }
        }
    }
}

