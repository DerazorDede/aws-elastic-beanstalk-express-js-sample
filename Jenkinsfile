pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/DerazorDede/aws-elastic-beanstalk-express-js-sample.git'
            }
        }
        
        stage('Install Dependencies and Test') {
            steps {
                script {
                    // Use a Node.js container for npm commands
                    docker.image('node:16').inside('-u root') {
                        sh 'npm install --save'
                        sh 'npm test'
                    }
                }
            }
        }
        
        stage('Security Scan') {
            steps {
                sh '''
                    # Download and run OWASP Dependency-Check
                    wget -q -O dependency-check.zip https://github.com/jeremylong/DependencyCheck/releases/download/v8.2.1/dependency-check-8.2.1-release.zip
                    unzip -q dependency-check.zip
                    ./dependency-check/bin/dependency-check.sh --project "MyApp" --scan . --format HTML --out ./reports --disableAssembly
                '''
            }
            post {
                always {
                    publishHTML (target: [
                        allowMissing: false,
                        alwaysLinkToLastBuild: true,
                        keepAll: true,
                        reportDir: 'reports',
                        reportFiles: 'dependency-check-report.html',
                        reportName: 'Dependency Check Report'
                    ])
                }
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("my-node-app:${env.BUILD_ID}")
                }
            }
        }
    }
}