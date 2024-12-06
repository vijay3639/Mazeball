pipeline {
    agent any
    stages {
        stage('Pull Code From GitHub') {
            steps {
                git 'https://github.com/vijay3639/Mazeball.git'
            }
        }
        stage('Build the Docker image') {
            steps {
                sh 'sudo docker build -t mazeimage /var/lib/jenkins/workspace/game-project'
                sh 'sudo docker tag mazeimage vijay3639/mazeimage:latest'
                sh 'sudo docker tag mazeimage vijay3639/mazeimage:${BUILD_NUMBER}'
            }
        }
        stage('Trivy Vulnerability Scan') {
            steps {
                script {
                    sh 'sudo trivy image vijay3639/mazeimage:latest'
                    sh 'sudo trivy image vijay3639/mazeimage:${BUILD_NUMBER}'
                }
            }
        }
        stage('Push the Docker image') {
            steps {
                sh 'sudo docker image push vijay3639/mazeimage:latest'
                sh 'sudo docker image push vijay3639/mazeimage:${BUILD_NUMBER}'
            }
        }
        stage('Deploy on Kubernetes') {
            agent { label 'k8s-server' }
            steps {
                sh 'sudo kubectl apply -f /var/lib/jenkins/workspace/game-project/pod.yml'
                sh 'sudo kubectl rollout restart deployment loadbalancer-pod'
            }
        }
    }
}

