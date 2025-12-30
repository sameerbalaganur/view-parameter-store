pipeline {
    agent any
    
    parameters {
        string(name: 'PARAMETER_NAME', defaultValue: '/dev/database/username', description: 'Parameter Store path to retrieve')
        choice(name: 'REGION', choices: ['us-east-1', 'us-west-2', 'eu-west-1'], description: 'AWS Region')
    }
    
    environment {
        AWS_REGION = "${params.REGION}"
        AWS_DEFAULT_REGION = "${params.REGION}"
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
                        --output text \
                        --region ${AWS_REGION} || echo 'Parameter not found'
                """
            }
        }

        stage('List All Parameters') {
            steps {
                echo "=== All Parameters in ${AWS_REGION} ==="
                sh """
                    aws ssm describe-parameters \
                        --query "Parameters[*].[Name,Type,LastModifiedDate]" \
                        --output table \
                        --region ${AWS_REGION}
                """
            }
        }

        stage('Get Parameters by Path') {
            steps {
                echo "=== Parameters under /dev/ path ==="
                sh """
                    aws ssm get-parameters-by-path \
                        --path '${params.PARAMETER_NAME}' \
                        --recursive \
                        --query "Parameters[*].[Name,Value]" \
                        --output table \
                        --region ${AWS_REGION} || echo "No parameters found under /dev/"
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