pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = '81355e3c-5f0a-4393-970d-0f1cf3713601'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
    }

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
            post {
                always {
                    junit '**/jest-test-results/*.xml'
                }
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

        stage('Run After build Tests') {
            parallel {
                stage('After Build Test') {
                    steps {
                        sh 'test -f build/index.html'
                    }
                }

                stage('E2E') {
                    agent {
                        docker {
                            image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                            reuseNode true
                        }
                    }
                    steps {
                        sh '''
                            npm install serve
                            node_modules/.bin/serve -s build &
                            sleep 10
                            npx playwright test --reporter=html
                        '''
                    }
                    post {
                        always {
                            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright HTML Report', reportTitles: '', useWrapperFileDirectly: true])
                        }
                    }
                }
            }
        }

        stage('Deploy') {
            agent {
                docker {
                    image 'node:22.14.0-alpine3.20'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    npm install netlify-cli
                    node_modules/.bin/netlify --version
                    echo "Deployin to production. Site ID: $NETLIFY_SITE_ID"
                    node_modules/.bin/netlify status
                '''
            }
        }
    }
}