pipeline {
    agent any

    environment {
        AWS_REGION = "ap-south-1"
        CLUSTER_NAME = "eks-demo-cluster"
    }

    stages {

        stage('Clone Repo') {
            steps {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/nil-2025/eks-terraform-jenkins.git']])
            }
        }

        stage('Terraform Init') {
            steps {
                dir('terraform') {
                    sh 'terraform init'
                }
            }
        }

        stage('Terraform Plan') {
            steps {
                dir('terraform') {
                    sh 'terraform plan'
                }
            }
        }

        stage('Terraform Apply') {
            steps {
                dir('terraform') {
                    sh 'terraform apply -auto-approve'
                }
            }
        }

        stage('Configure EKS') {
            steps {
                sh '''
                aws eks update-kubeconfig \
                --region $AWS_REGION \
                --name $CLUSTER_NAME
                '''
            }
        }

        stage('Deploy App') {
            steps {
                dir('k8s') {
                    sh 'kubectl apply -f deployment.yaml'
                    sh 'kubectl apply -f service.yaml'
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                sh 'kubectl get pods'
                sh 'kubectl get svc'
            }
        }
    }
}
