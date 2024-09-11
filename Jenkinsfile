pipeline {
    agent any
        environment {
        S3_BUCKET = 'rajtal-votingapp'
        S3_PATH = 'helm-charts'
    }
    
    stages {
        stage('Clone Repository') {
            steps {
                sh 'git clone https://github.com/RKDevops1234/voting-app.git'
            }
        }
        stage('Package Helm chart') {
            steps {
                sh 'cd voting-app/db/charts && helm package .'
            }
        }

         stage('Upload Helm Chart to S3') {
            steps {
                withCredentials(credentials: 'aws-credentials-id') {
                    script {
                        def chartFile = sh(script: 'ls voting-app/db/charts/*.tgz', returnStdout: true).trim()
                        sh "aws s3 cp ${chartFile} s3://${S3_BUCKET}/${S3_PATH}/${chartFile}"
                    }
                }
             }
        }
    }
}

// aws s3 cp db-1.0.0.tgz s3://rajtal-votingapp/helm-charts/
// helm repo index . --url https://rajtal-votingapp.s3.amazonaws.com/helm-charts/
//aws s3 cp index.yaml s3://rajtal-votingapp/helm-charts/
//helm repo add my-repo https://rajtal-votingapp.s3.amazonaws.com/helm-charts/

