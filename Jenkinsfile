pipeline {
    agent any
    
    parameters {
        string(name: 'PARAMETER_NAME', defaultValue: '/dev/database/username', description: 'Parameter Store path to retrieve')
        choice(name: 'REGION', choices: ['us-east-1', 'us-west-2', 'eu-west-1'], description: 'AWS Region')
    }
    
    environment {
        AWS_REGION = "${params.REGION}"
        AWS_DEFAULT_REGION = "${params.REGION}"
        AWS_PAGER=""
    }
    
    stages {
        stage('Checkout') {
            steps {
                echo "Code checked out from GitHub"
                echo "Branch: ${env.GIT_BRANCH}"
                echo "Commit: ${env.GIT_COMMIT}"
            }
        }
        
        stage('View Single Parameter') {
            steps {
                echo "=== Retrieving Parameter using EC2 IAM Role ==="
                sh """
                    aws ssm get-parameter \
                        --name '${params.PARAMETER_NAME}' \
                        --with-decryption \
                        --output text \
                        --region ${AWS_REGION} || echo 'Parameter not found'
                """
            }
        }
    }
    
    post {
        success {
            echo "Pipeline completed successfully!"
        }
        failure {
            echo "Pipeline failed. Check the logs above."
        }
    }
}