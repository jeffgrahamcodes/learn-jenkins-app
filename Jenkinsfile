pipeline {
    agent any

    stages {
        stage('Build') {
          agent{
            docker {
              image 'node:18-alpine'
              reuseNode true
            }
          }
          steps {
            sh '''
              ls -la
              node --version
              npm --version
              npm ci
              npm run build
              ls -la
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
              echo "Test Stage"
              if test -f build/index.html; then
                echo "file exists"
              else
                echo "file does not exist"
                exit 1
              fi
              npm test
            '''
          }
        }
    }
}
