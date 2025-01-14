pipeline {
    agent any
    tools {
        maven 'maven'
    }
    stages {
        stage('Build Maven') {
            steps {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/Sanjay6938/devops-automation.git']])
                sh 'mvn clean install'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t johnu4693/devops-integration:latest .'
                    sh 'docker images' // Verify the image exists
                }
            }
        }
        stage('Push Image to Hub') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'Dockercred1', variable: 'DOCKER_PASSWORD')]) {
                        sh 'docker login -u johnu4693 -p ${DOCKER_PASSWORD}'
                    }
                    sh 'docker push johnu4693/devops-integration:latest'
                }
            }
        }
        stage('Deploy to k8s') {
            steps {
                script {
                    withCredentials([file(credentialsId: 'k8sConfig', variable: 'KUBECONFIG')]) {
                        sh '''
                        export KUBECONFIG=${KUBECONFIG}
                        kubectl apply -f deploymentservice.yaml
                        kubectl get pods
                        '''
                    }
                }
            }
        }
    }
}
