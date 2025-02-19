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
    }

    post {
        always {
            junit 'tests-results/junit.xml'
        }
    }
}