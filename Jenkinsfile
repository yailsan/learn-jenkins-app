pipeline {
    agent any

    stages {
        agent {
            docker {
                image: 'node:22.14.0-alpine3.20'
                reuseNode: true
            }
        }
        stage('Build') {
            steps {
                sh '''
                    ls -la
                    node --version
                    npm --version
                    npm ci
                    npm run build
                    ls -las
                '''
            }
        }
    }
}