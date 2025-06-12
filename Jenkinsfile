pipeline {
    agent any

    environment {
        // adjust region if necessary
        AWS_DEFAULT_REGION = 'us-east-1'
    }

    stages {
        stage('Clean workspace') {
            steps { cleanWs() }
        }

        stage('Configure kubectl for EKS') {
            steps {
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'eks-deploy']]) {
                    sh 'aws eks update-kubeconfig --name basic-eks --region $AWS_DEFAULT_REGION'
                }
            }
        }

        stage('List cluster resources') {
            steps {
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'eks-deploy']]) {
                    sh 'kubectl get nodes,pods,svc --all-namespaces'
                }
            }
        }

        stage('Export AWS Account ID') {
            steps {
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'eks-deploy']]) {
                    sh '''#!/bin/bash
                        ACCOUNT_ID=$(aws sts get-caller-identity --query Account --output text)
                        echo "ACCOUNT_ID=$ACCOUNT_ID"
                    '''
                }
            }
        }
    }
}
