pipeline {
    agent any

    environment {
        DOCKER_HUB_REGISTRY = 'milosky'  // Replace with your Docker registry
        DOCKER_IMAGE = 'user-management'  // Replace with your Docker image name
        BUILD_ID = 'latest'
        KUBERNETES_CONFIG = credentials('my_kubernetes')  // Replace with your Kubernetes config credentials ID in Jenkins
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/MILODIAZ/devops-user-management.git'  // Replace with your repository URL
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'npm run test'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE}:${BUILD_ID}")
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: "${docker_hub_creds}", passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                        sh """
                        echo "${DOCKER_PASSWORD}" | docker login -u "${DOCKER_USERNAME}" --password-stdin
                        docker push ${DOCKER_IMAGE}:${BUILD_ID}
                        """
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    withKubeConfig([credentialsId: "${KUBERNETES_CONFIG}"]) {
                        sh """
                        kubectl set image deployment/your-deployment-name your-container-name=${DOCKER_REGISTRY}/${DOCKER_IMAGE}:${BUILD_ID}  // Replace with your deployment and container names
                        kubectl apply -f k8s/deployment.yaml  // Adjust the path to your Kubernetes deployment YAML file
                        """
                    }
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}

