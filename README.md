EKS PIPELINE Script

'''
      pipeline {
          agent any
          environment {
              // Set AWS credentials if using Jenkins credentials plugin
              AWS_ACCESS_KEY_ID = credentials('seytan')
              AWS_SECRET_ACCESS_KEY = credentials('seytan')
              AWS_DEFAULT_REGION = 'ap-south-1'  // Set your desired AWS region
          }
          parameters {
              choice(
                  name: 'action',
                  choices: ['apply', 'destroy'],
                  description: 'Choose the Terraform action to perform'
              )
          }
          stages {
              stage('Checkout from Git') {
                  steps {
                      echo "Checking out the code from Git..."
                      git branch: 'main', url: 'https://github.com/Gaurav1251/Netflix-cicd.git'
                  }
              }
              
              stage('Check AWS Credentials') {
                  steps {
                      script {
                          echo "Checking AWS credentials..."
                          // Check AWS identity to verify credentials
                          sh 'aws sts get-caller-identity'
                      }
                  }
              }
      
              stage('Terraform version') {
                  steps {
                      echo "Checking Terraform version..."
                      sh 'terraform --version'
                  }
              }
      
              stage('Terraform init') {
                  steps {
                      echo "Initializing Terraform..."
                      dir('Terraformm') {
                          sh 'terraform init'
                      }
                  }
              }
      
              stage('Terraform validate') {
                  steps {
                      echo "Validating Terraform configuration..."
                      dir('Terraformm') {
                          sh 'terraform validate'
                      }
                  }
              }
      
              stage('Terraform plan') {
                  steps {
                      echo "Running Terraform plan..."
                      dir('Terraformm') {
                          sh 'terraform plan'
                      }
                  }
              }
      
              stage('Terraform apply/destroy') {
                  steps {
                      echo "Applying or Destroying Terraform configuration..."
                      dir('Terraformm') {
                          // Use the parameter to determine the action
                          sh "terraform ${params.action} --auto-approve"
                      }
                  }
              }
          }
          post {
              failure {
                  echo "Pipeline failed. Check the logs for errors."
              }
              success {
                  echo "Pipeline executed successfully!"
              }
          }
      }  '''
