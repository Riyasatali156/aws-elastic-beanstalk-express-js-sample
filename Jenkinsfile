pipeline {
    agent {
        docker {
            image 'node:16'
            args '-u root'  // Run as root to avoid permission issues
        }
    }
	
    environment {
        SNYK_TOKEN = credentials('SNYK_TOKEN')  // Get Snyk token from Jenkins credentials
    }
	
    stages {
        stage('Install Dependencies') {
            steps {
                script {
                    echo 'Starting to install project dependencies using npm...'
                    sh 'npm install --save'  // Install dependencies
                    echo 'Dependencies installed successfully.'
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    echo 'Starting to run project tests...'
                    // Run the tests if any are defined in package.json
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
                    sh 'npm install -g snyk'  // Install Snyk globally
                    echo 'Snyk installed successfully.'
                    
                    // Authenticate Snyk using the SNYK_TOKEN
                    sh "snyk auth ${SNYK_TOKEN}"

                    echo 'Running Snyk security scan with severity threshold set to high...'
                    // Run Snyk security scan, fail the build if upgradable vulnerabilities are found
                    def snykResult = sh(script: 'snyk test --severity-threshold=high --fail-on=upgradable', returnStatus: true)
                    if (snykResult != 0) {
                        error 'Upgradable vulnerabilities found! Failing the pipeline.'
                    } else {
                        echo 'Snyk scan completed. No upgradable vulnerabilities found.'
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
