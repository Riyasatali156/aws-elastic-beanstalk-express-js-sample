pipeline {
    agent { docker { image 'node:16' } }
    stages {
        stage('Install dependencies') {
            steps {
                sh 'node --version'
                sh 'npm --version'
                sh 'npm install --save'
                sh 'npm install express@4.20.0'
            }
        }
        stage('Snyk Scan') {
            steps {
                echo 'Scanning...'
                sh '''
                npm install snyk -g
                snyk auth 743916bb-4361-4f05-b84d-ceeeee17d530
                snyk test --severity-threshold=high
                '''
            }
        }
    }
}
