pipeline {
    agent {
        docker {
            image 'node:16'
            args '-u root'
        }
    }

    environment {
        SNYK_TOKEN = credentials('snyk-token')
    }
    
    stages {
        stage('Install Dependencies') {
            steps {
                script {
                    echo 'Starting to Install Project Dependencies using NPM...'
                    sh 'npm install --save'
                    echo 'Dependencies Intalled Successfully.'
                }
            }
        }
        
        stage('Run Tests') {
            steps {
                script {
                    echo 'Starting to Run Project Tests...'
                    sh 'npm test'
                    echo 'Tests Completed Successfully.'
                }
            }
        }
        
        stage('Snyk Security Scan') {
            steps {
                script {
		    echo 'Starting Snyk Security Scan...'
                    sh 'npm install -g snyk'
		    sh 'npm install express@4.20.0 --save'
                    echo 'Snyk Installed Successfully.'
                    
		    // Authenticate Snyk if necessary (add your Snyk Auth Token to the ENvironment Variables)
		    // sh 'snyk auth $SNYK_TOKEN'
		    
		    echo 'Running Snyk Security Scan with Severity Threshold Set to High...'
		    sh 'snyk test --severity-threshold=high || true'
		    echo 'Snyk Scan Completed. Check for Any Critical Vulnerabilities.'
		    
                    def result = sh(script: 'snyk test', returnStatus: true)
                    if (result != 0) {
                        error "Critical vulnerabilities found! Halting the pipeline."
                    }
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
