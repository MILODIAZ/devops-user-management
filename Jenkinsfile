pipeline {
    agent any
    stages {
        stage('SCM NodeApp') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/MILODIAZ/devops-user-management.git']]])
            }
        }  
        stage('Test NodeApp') {
            agent{
                docker { image 'node:20'}
            }
            steps {
                sh 'npm install'
                sh 'npm run test'
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
        stage('Apply Kubernetes files') {
            steps{
                withKubeConfig([credentialsId: 'my_kubernetes', serverUrl: 'https://192.168.49.2:8443']) {
                    sh 'kubectl apply -f deployment.yaml && kubectl rollout restart deployment user-management'
                }
            }
        }
    }
}
