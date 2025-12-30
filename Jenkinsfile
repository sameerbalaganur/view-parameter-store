pipeline {
    agent any
    
    parameters {
        string(name: 'PARAMETER_NAME', defaultValue: '/dev/database/username', description: 'Parameter Store path to retrieve')
        choice(name: 'REGION', choices: ['us-east-1', 'us-west-2', 'eu-west-1'], description: 'AWS Region')
    }
    
    environment {
        AWS_REGION = "${params.REGION}"
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
                script {
                    withAWS(credentials: 'aws-credentials', region: "${AWS_REGION}") {
                        echo "=== Retrieving Parameter ==="
                        def paramValue = sh(
                            script: "aws ssm get-parameter --name '${params.PARAMETER_NAME}' --query 'Parameter.Value' --output text 2>/dev/null || echo 'Parameter not found'",
                            returnStdout: true
                        ).trim()
                        
                        echo "Parameter: ${params.PARAMETER_NAME}"
                        echo "Value: ${paramValue}"
                    }
                }
            }
        }
        
        stage('List All Parameters') {
            steps {
                script {
                    withAWS(credentials: 'aws-credentials', region: "${AWS_REGION}") {
                        echo "=== All Parameters in ${AWS_REGION} ==="
                        sh '''
                            aws ssm describe-parameters \
                                --query "Parameters[*].[Name,Type,LastModifiedDate]" \
                                --output table
                        '''
                    }
                }
            }
        }
        
        stage('Get Parameters by Path') {
            steps {
                script {
                    withAWS(credentials: 'aws-credentials', region: "${AWS_REGION}") {
                        echo "=== Parameters under /dev/ path ==="
                        sh '''
                            aws ssm get-parameters-by-path \
                                --path "/dev" \
                                --recursive \
                                --query "Parameters[*].[Name,Value]" \
                                --output table || echo "No parameters found under /dev/"
                        '''
                    }
                }
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