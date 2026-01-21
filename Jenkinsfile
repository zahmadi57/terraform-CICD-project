pipeline {
    agent any

    parameters {
        string(
            name: 'BUCKET_NAME',
            defaultValue: 'jenkins-terraform-demo',
            description: 'Name of the S3 bucket'
        )
    }

    environment {
        AWS_DEFAULT_REGION = 'us-east-1'
        TERRAFORM_BIN      = '/usr/bin/terraform'
    }

    stages {

        stage('Terraform Init') {
            steps {
                withCredentials([
                    string(credentialsId: 'AWS_ACCESS_KEY_ID', variable: 'AWS_ACCESS_KEY_ID'),
                    string(credentialsId: 'AWS_SECRET_ACCESS_KEY', variable: 'AWS_SECRET_ACCESS_KEY')
                ]) {
                    sh "${TERRAFORM_BIN} init"
                }
            }
        }

        stage('Terraform Validate') {
            steps {
                withCredentials([
                    string(credentialsId: 'AWS_ACCESS_KEY_ID', variable: 'AWS_ACCESS_KEY_ID'),
                    string(credentialsId: 'AWS_SECRET_ACCESS_KEY', variable: 'AWS_SECRET_ACCESS_KEY')
                ]) {
                    sh "${TERRAFORM_BIN} validate"
                }
            }
        }

        stage('Terraform Plan') {
            steps {
                withCredentials([
                    string(credentialsId: 'AWS_ACCESS_KEY_ID', variable: 'AWS_ACCESS_KEY_ID'),
                    string(credentialsId: 'AWS_SECRET_ACCESS_KEY', variable: 'AWS_SECRET_ACCESS_KEY')
                ]) {
                    sh "${TERRAFORM_BIN} plan -var=\"bucket_name=${BUCKET_NAME}\""
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
                    string(credentialsId: 'AWS_ACCESS_KEY_ID', variable: 'AWS_ACCESS_KEY_ID'),
                    string(credentialsId: 'AWS_SECRET_ACCESS_KEY', variable: 'AWS_SECRET_ACCESS_KEY')
                ]) {
                    sh "${TERRAFORM_BIN} apply -auto-approve -var=\"bucket_name=${BUCKET_NAME}\""
                }
            }
        }
    }

    post {
        success {
            echo 'Terraform applied successfully!'
        }
        failure {
            echo 'Pipeline failed. Check logs for details.'
        }
        always {
            echo 'Pipeline finished.'
        }
    }
}
