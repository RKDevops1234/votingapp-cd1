pipeline {
    agent any

    environment {
        S3_BUCKET = 'rajtal-votingapp'
        S3_PATH = 'helm-charts'
        AWS_REGION = 'us-east-1' // Specify your AWS region here
    }

    stages {
        stage('Clone Repository') {
            steps {
                sh 'git clone https://github.com/RKDevops1234/voting-app.git'
            }
        }

        stage('Package Helm Chart') {
            steps {
                dir('voting-app/db/charts') {
                    sh 'helm package .'
                }
            }
        }

        stage('Upload Helm Chart to S3') {
            steps {
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws-credentials-id']]) {
                    script {
                        // List the Helm chart file
                        def chartFile = sh(script: 'ls voting-app/db/charts/*.tgz', returnStdout: true).trim()
                        // Upload the Helm chart to S3
                        sh "aws s3 cp ${chartFile} s3://${S3_BUCKET}/${S3_PATH}/ --region ${AWS_REGION}"
                    }
                }
            }
        }
    }

    post {
        always {
            // Clean up workspace
            sh 'rm -rf $WORKSPACE/*'
        }
    }
}


// aws s3 cp db-1.0.0.tgz s3://rajtal-votingapp/helm-charts/
// helm repo index . --url https://rajtal-votingapp.s3.amazonaws.com/helm-charts/
//aws s3 cp index.yaml s3://rajtal-votingapp/helm-charts/
//helm repo add my-repo https://rajtal-votingapp.s3.amazonaws.com/helm-charts/

