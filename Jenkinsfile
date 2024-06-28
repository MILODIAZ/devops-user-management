pipeline {
    agent any
    stages {
        stage('Build Maven') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/MILODIAZ/devops-user-management.git']]])
            }
        }
        stage('Install NPM Dependencies') {
            steps {
                script {
                    sh 'npm install'
                }
            }
        }
        stage('Run NPM Tests') {
            steps {
                script {
                    sh 'npm run test'
                }
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t milosky/user-management:latest .'
                }
            }
        }
        stage('Deploy Docker Image') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'docker_hub_creds', variable: 'dockerhubpwd')]) {
                        sh 'docker login -u milosky -p ${dockerhubpwd}'
                    }
                    sh 'docker push milosky/user-management:latest'
                }
            }
        }
        stage('Deploy App on k8s') {
            steps {
                withCredentials([
                    string(credentialsId: 'my_kubernetes', variable: 'api_token')
                ]) {
                    sh 'kubectl --token $api_token --server https://192.168.49.2:8443 --insecure-skip-tls-verify=true apply -f deployment.yaml'
                }
            }
        }
    }
}
