pipeline {
    agent any

    stages {
        stage('Clone') {
            steps {
                git branch:'main', url: 'https://github.com/DoubleF3/Pipeline.git'
            }
        }
        stage('Check & parse file') {
            steps {
                script {
                    if(fileExists('aws_region.yml')) {
                        echo "File aws_region.yml found!"
                    }
                }
                script {
                    region = sh (
                    'yq \'.Regions.region\' aws_region.yml'
                    returnStdout: true
                ).trim()
                sh "export AWS_DEFAULT_REGION=${region}"
                }
            }
        }
        
        stage('Terraform validate') {
            when {
                expression {
                    aws_region.yml == 'true' 
                    }
                }
            steps {
                script {
                    sh 'terraform init -input=false'
                    sh "terraform validate -input=false"
                }
            }
        }
        stage('Terraform plan and store') {
            steps {
                script {
                sh 'terraform plan -out=tfplan -input=false'
                }
            }
        }
        stage('Terraform apply form stored file') {
            steps {
                script{
                sh 'terraform apply -input=false -auto-approve tfplan'
                }
            }
        }
    }
}