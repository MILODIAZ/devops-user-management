pipeline {
    agent any
    stages {
        stage('Build NodeApp') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/MILODIAZ/devops-user-management.git']]])
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
        stage('Deploying NodeJs container to Kubernetes') {
            steps {
                // Apply Kubernetes deployment using the Kubernetes service account
                withCredentials([string(credentialsId: 'my_kubernetes', variable: 'api_token')]) {
                    bat """
                        kubectl --token $api_token --server https://192.168.103.2:8443  --insecure-skip-tls-verify=true apply -f deployment.yaml 
                    """
                }
            }
        }
    }
}
