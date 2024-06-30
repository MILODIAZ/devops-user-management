pipeline {
    agent {
        kubernetes {
            label 'my-kubernetes-agent'
        }
    }
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
    }
}
