pipeline {
    agent any

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                // Ensure a clean workspace to avoid permission issues
                deleteDir()
                sh '''
                    echo "🛠️ Starting Build..."
                    node --version
                    npm --version
                    npm ci
                    npm run build
                '''
            }
        }

        stage('Test') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    echo "🧪 Running Unit Tests..."
                    mkdir -p tmp-test-results
                    chmod -R 777 tmp-test-results
                    JEST_JUNIT_OUTPUT=tmp-test-results/junit.xml npm test
                '''
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
                    echo "🎭 Running E2E Tests..."
                    rm -rf node_modules
                    npm install serve
                    node_modules/.bin/serve -s build &
                    sleep 10
                    npx playwright test
                '''
            }
        }
    }

    post {
        always {
            echo "📦 Publishing Test Results..."
            junit 'tmp-test-results/junit.xml'
        }
    }
}