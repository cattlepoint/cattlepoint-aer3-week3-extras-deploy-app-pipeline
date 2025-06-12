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

        stage('Clone repo') {
            steps {
                sh 'git clone https://github.com/cattlepoint/cattlepoint-aer3-week2.git'
            }
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

        stage('Apply Kubernetes manifests') {
            steps {
                dir('cattlepoint-aer3-week2') {
                    sh 'cat cattlepoint-deployment.v1.yaml | envsubst | kubectl apply -f -'
                }
            }
        }
    }
}
