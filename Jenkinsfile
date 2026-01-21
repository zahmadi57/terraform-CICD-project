pipeline {
    agent any
 
    // Optional: allow user to enter bucket name
    parameters {
        string(name: 'BUCKET_NAME', defaultValue: 'jenkins-terraform-demo', description: 'Name of the S3 bucket')
    }
 
    environment {
        // AWS credentials stored in Jenkins (IDs: aws-access-key & aws-secret-key)
        AWS_ACCESS_KEY_ID     = credentials('aws-access-key')
        AWS_SECRET_ACCESS_KEY = credentials('aws-secret-key')
        TERRAFORM_BIN         = "/usr/bin/terraform" // Correct path for ARM64 Jenkins server
    }
 
    stages {
 
        stage('Checkout Code') {
            steps {
                // Explicitly checkout main branch
                git branch: 'main',
                    url: 'https://github.com/omar-masood/terraform-jenkins-project.git'
            }
        }
 
        stage('Terraform Init') {
            steps {
                sh "${env.TERRAFORM_BIN} init"
            }
        }
 
        stage('Terraform Validate') {
            steps {
                sh "${env.TERRAFORM_BIN} validate"
            }
        }
 
        stage('Terraform Plan') {
            steps {
                sh "${env.TERRAFORM_BIN} plan -var 'bucket_name=${params.BUCKET_NAME}'"
            }
        }
 
        stage('Approval') {
            steps {
                input message: 'Do you want to apply this Terraform plan?', ok: 'Approve & Apply'
            }
        }
 
        stage('Terraform Apply') {
            steps {
                sh "${env.TERRAFORM_BIN} apply -var 'bucket_name=${params.BUCKET_NAME}'"
            }
        }
    }
 
    post {
        always {
            echo 'Pipeline finished!'
        }
        success {
            echo 'Terraform applied successfully!'
        }
        failure {
            echo 'Pipeline failed. Check logs for errors.'
        }
    }
}
