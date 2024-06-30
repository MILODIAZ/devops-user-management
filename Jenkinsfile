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
        stage('Deploying NodeJs container to Kubernetes') {
            steps {
                kubernetesDeploy(configs: 'deployment.yaml')
            }
        }
    }
}
