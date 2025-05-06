pipeline {
    agent any

    environment {
        IMAGE_NAME = "kareem9236/abc-retail"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/KareemAbdelaziz92/IGP.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean compile'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Package') {
            steps {
                sh 'mvn package'
            }
        }

        stage('Docker Build & Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    script {
                        echo "Logging in to Docker Hub..."
                        sh '''
                            echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        '''
                        echo "Building Docker image..."
                        sh "docker build -t ${IMAGE_NAME}:latest ."
                        echo "Pushing image to Docker Hub..."
                        sh "docker push ${IMAGE_NAME}:latest"
                    }
                }
            }
        }

        stage('Ansible Deployment') {
            steps {
                sh 'ansible-playbook -i inventory.ini deploy.yaml'
            }
        }

        stage('Kubernetes Deployment') {
            steps {
                sh 'kubectl apply -f K8s/deployment.yaml'
                sh 'kubectl apply -f K8s/service.yaml'
            }
        }
    }

    post {
        success {
            echo "Build and deployment completed successfully."
        }
        failure {
            echo "Build or deployment failed!"
        }
    }
}
