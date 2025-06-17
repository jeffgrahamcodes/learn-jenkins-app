pipeline {
    agent any

    environment {
        REACT_APP_VERSION = "1.0.$BUILD_ID"
        AWS_DEFAULT_REGION ='us-east-1'
    }

    stages {

        stage('Deploy to AWS') {
          agent {
            docker {
              image 'amazon/aws-cli'
              reuseNode true
              args "--entrypoint=''"
            }
          }

          steps{
            withCredentials([usernamePassword(credentialsId: 'my-aws', passwordVariable: 'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')]) {
              sh '''
                aws --version
                aws ecs register-task-definition --cli-input-json file://aws/task-definition-prod.json
                aws ecs update-service --cluster learn-jenkins-app-cluster-prod --service learn-jenkins-app-task-definition-prod-service --task-definition learn-jenkins-app-task-definition-prod:2
              '''
            }
          }
        }

        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    echo "Small change"
                    ls -la
                    node --version
                    npm --version
                    npm ci
                    npm run build
                    ls -la
                '''
            }
        }

    } // end stages
} // end pipeline