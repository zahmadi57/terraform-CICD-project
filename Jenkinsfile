pipeline {
    agent any

    // Optional: allow user to enter bucket name
    parameters {
        string(
            name: 'BUCKET_NAME',
            defaultValue: 'jenkins-terraform-demo-${env.BUILD_NUMBER}',
            description: 'Name of the S3 bucket (must be globally unique)'
        )
    }

    environment {
        AWS_DEFAULT_REGION = 'us-east-1'
        TERRAFORM_BIN      = '/usr/bin/terraform'
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/zahmadi57/terraform-CICD-project.git'
            }
        }

        stage('Terraform Init') {
            steps {
                withCredentials([
                    string(credentialsId: 'aws-access-key', variable: 'AWS_ACCESS_KEY_ID'),
                    string(credentialsId: 'aws-secret-key', variable: 'AWS_SECRET_ACCESS_KEY')
                ]) {
                    sh "${TERRAFORM_BIN} init"
                }
            }
        }

        stage('Terraform Validate') {
            steps {
                withCredentials([
                    string(credentialsId: 'aws-access-key', variable: 'AWS_ACCESS_KEY_ID'),
                    string(credentialsId: 'aws-secret-key', variable: 'AWS_SECRET_ACCESS_KEY')
                ]) {
                    sh "${TERRAFORM_BIN} validate"
                }
            }
        }

        stage('Terraform Plan') {
            steps {
                withCredentials([
                    string(credentialsId: 'aws-access-key', variable: 'AWS_ACCESS_KEY_ID'),
                    string(credentialsId: 'aws-secret-key', variable: 'AWS_SECRET_ACCESS_KEY')
                ]) {
                    sh "${TERRAFORM_BIN} plan -var=\"bucket_name=${params.BUCKET_NAME}\""
                }
            }
        }

        stage('Approval') {
            steps {
                input message: 'Approve Terraform Apply?', ok: 'Apply'
            }
        }

        stage('Terraform Apply') {
            steps {
                withCredentials([
                    string(credentialsId: 'aws-access-key', variable: 'AWS_ACCESS_KEY_ID'),
                    string(credentialsId: 'aws-secret-key', variable: 'AWS_SECRET_ACCESS_KEY')
                ]) {
                    sh "${TERRAFORM_BIN} apply -auto-approve -var=\"bucket_name=${params.BUCKET_NAME}\""
                }
            }
        }
    }

    post {
        success {
            echo "Terraform applied successfully! Bucket: ${params.BUCKET_NAME}"
        }
        failure {
            echo 'Pipeline failed. Check logs for details.'
        }
        always {
            echo 'Pipeline finished.'
        }
    }
}
