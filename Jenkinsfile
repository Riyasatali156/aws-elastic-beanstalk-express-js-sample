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
                    echo 'Starting to install project dependencies using npm...'
                    sh 'npm install --save'
                    echo 'Dependencies installed successfully.'
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    echo 'Starting to run project tests...'
                    def testExists = sh(script: "npm run | grep -q 'test'", returnStatus: true)
                    if (testExists == 0) {
                        sh 'npm test'
                        echo 'Tests completed successfully.'
                    } else {
                        echo 'No test script found, skipping tests.'
                    }
                }
            }
        }

        stage('Snyk Security Scan') {
            steps {
                script {
                    echo 'Starting Snyk security scan...'
                    sh 'npm install -g snyk'
                    echo 'Snyk installed successfully.'
                    
                    sh "snyk auth ${SNYK_TOKEN}"

                    echo 'Running Snyk security scan with severity threshold set to high...'
                    // Allow vulnerabilities below the critical level to pass the pipeline
                    def snykResult = sh(script: 'snyk test --severity-threshold=high --fail-on=patchable', returnStatus: true)
                    if (snykResult != 0) {
                        error 'Patchable vulnerabilities found! Failing the pipeline.'
                    } else {
                        echo 'Snyk scan completed. No patchable vulnerabilities found.'
                    }
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline execution finished. Check the above steps for results.'
        }

        failure {
            echo 'Pipeline execution failed. Please review the logs above for details.'
        }
    }
}
