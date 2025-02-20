pipeline {
    agent any

    stages {
        stage('Test') {
            agent {
                docker {
                    image 'node:22.14.0-alpine3.20'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    node --version
                    npm --version
                    npm run test
                '''
            }
        }

        stage('Build') {
            agent {
                docker {
                    image 'node:22.14.0-alpine3.20'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    ls -la
                    npm ci
                    npm run build
                    ls -las
                '''
            }
        }

        stage('After Build Test') {
            steps {
                sh 'test -f build/index.html'
            }
        }

        stage('E2E') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.50.1-noble'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    npm install -g serve
                    serve -s build
                    npx playwright test
                '''
            }
        }
    }

    post {
        always {
            junit '**/test-results/*.xml'
        }
    }
}