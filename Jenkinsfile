pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-1' // Specify your AWS region here
        CLUSTER_NAME = 'ed-eks-01' // Change this to your EKS cluster name
        S3_BUCKET = 'rajtal-votingapp'
        S3_PATH = 'helm-charts'
        CHART_PATH = 'helm-charts'
        CHART_VERSION = "1.1.3"
        NAMESPACE = 'dev' // Kubernetes namespace to deploy into
      //  CHART_PATH = 'helm-charts/my-chart.tgz' // Path to the Helm chart in S3
     //   CHART_NAME = 'my-chart' // Helm chart name
        
        //AWS_CLI_VERSION = '2.4.0' // Version of AWS CLI you want to install
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout your repository if needed
                checkout scm
            }
        }

        stage('Set up AWS Credentials') {
            steps {
                // Configure AWS CLI with credentials
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws-credentials-id']]) {
                    //sh 'aws configure set aws_access_key_id $AWS_ACCESS_KEY_ID'
                    // sh 'aws configure set aws_secret_access_key $AWS_SECRET_ACCESS_KEY'
                    sh 'aws configure set region $AWS_REGION'
                }
            }
        }

        stage('Update kubeconfig') {
            steps {
                // Update kubeconfig to access the EKS cluster
                sh 'aws eks update-kubeconfig --region $AWS_REGION --name $CLUSTER_NAME'
            }
        }

        
        stage('Download Helm Charts') {
            steps {
                script {
                    // Define an array of chart names
                    def charts = ['db', 'redis', 'worker','vote']
            
                    // Iterate over each chart name
                        charts.each { chartName ->
                     // Construct the S3 path for the current chart
                    def s3Path = "s3://$S3_BUCKET/$CHART_PATH/${chartName}-${CHART_VERSION}.tgz"
                
                        // Download the chart from S3
                    sh "aws s3 cp ${s3Path} ${chartName}-${CHART_VERSION}.tgz"
                    }
                 }
        }
    }

        stage('Install/Upgrade Helm Charts') {
            steps {
             script {
             // Define an array of chart names
              def charts = ['db', 'redis', 'worker','vote']
            
             // Iterate over each chart name
             charts.each { chartName ->
                    // Install or upgrade the Helm chart
                sh """
                helm upgrade --install ${chartName} ${chartName}-${CHART_VERSION}.tgz \
                    --namespace ${NAMESPACE} 
                    // --values ${chartName}-values.yaml
                   """
             }
            }
        }
    }

}

    post {
        success {
            echo 'Helm chart successfully deployed'
        }
        failure {
            echo 'Failed to deploy Helm chart'
        }
    }
}
