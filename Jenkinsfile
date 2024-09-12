pipeline {
    agent {
        docker {
            image 'node:16'  // Use Node 16 as the build agent
            args '-u root'   // Run as root to avoid permission issues
        }
    }

    environment {
        SNYK_TOKEN = credentials('snyk_token')  // Reference the Snyk token stored in Jenkins credentials
    }

    stages {
        stage('Install Dependencies') {
            steps {
                echo 'Installing dependencies...'
                sh 'npm install'  // Install project dependencies using npm
            }
        }

        stage('Build') {
            steps {
                echo 'Building the application...'
                sh 'npm run build'  // Build the application
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
                sh 'npm test'  // Run the tests
            }
        }

        stage('Security Scan') {
            steps {
                echo 'Running Snyk security scan...'
                // Snyk tool to scan for vulnerabilities using the token from Jenkins credentials
                sh 'npm install -g snyk'  // Install Snyk globally
                sh 'snyk auth ${SNYK_TOKEN}'  // Authenticate Snyk using the token
                sh 'snyk test --severity-threshold=high'  // Perform Snyk security test
            }
        }
    }

    post {
        always {
            echo 'Pipeline execution complete.'
        }
        failure {
            echo 'Build failed due to errors or vulnerabilities.'
        }
        success {
            echo 'Build and tests completed successfully.'
        }
    }
}
