pipeline {
    agent {
        docker {
            image 'node:16'
            args '-u root'
        }
    }

    environment {
        SNYK_TOKEN = credentials('SNYK_TOKEN')
    }
    
    stages {
        stage('Install Dependencies') {
            steps {
                script {
                    echo 'Starting to Install Project Dependencies using NPM...'
                    def npmInstallResult = sh(script: 'npm install --save', returnStatus: true)
                    if (npmInstallResult != 0) {
                        error "npm install failed!"
                    }
                    echo 'Dependencies Installed Successfully.'
                }
            }
        }
        
        stage('Run Tests') {
            steps {
                script {
                    echo 'Starting to Run Project Tests...'
                    def testResult = sh(script: 'npm test', returnStatus: true)
                    if (testResult != 0) {
                        error "Tests failed!"
                    }
                    echo 'Tests Completed Successfully.'
                }
            }
        }
        
        stage('Snyk Security Scan') {
            steps {
                script {
                    echo 'Starting Snyk Security Scan...'
                    sh 'npm install -g snyk'
                    echo 'Snyk Installed Successfully.'
                    
                    // Authenticate Snyk
                    sh "echo ${SNYK_TOKEN} | snyk auth"
                    
                    echo 'Running Snyk Security Scan with Severity Threshold Set to High...'
                    def snykResult = sh(script: 'snyk test --severity-threshold=high', returnStatus: true)
                    if (snykResult != 0) {
                        error "Critical vulnerabilities found! Halting the pipeline."
                    }
                    echo 'Snyk Scan Completed. Check for Any Critical Vulnerabilities.'
                }
            }
        }
    }
    post {
        always {
            echo 'Pipeline Execution Finished. Check the Above Steps for Results.'
        }
        failure {
            echo 'Pipeline Execution Failed. Please Review the Logs for Details.'
        }
    }
}
