pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-creds')  // ID Jenkins des credentials Docker Hub
        IMAGE_NAME = "tondockerhubusername/my-java-app"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    credentialsId: 'github-creds',
                    url: 'https://github.com/OnsKoukiPro/TPDevOps.git'
            }
        }


        stage('Build with Maven') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${IMAGE_NAME}:latest ."
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    sh "echo ${DOCKERHUB_CREDENTIALS_PSW} | docker login -u ${DOCKERHUB_CREDENTIALS_USR} --password-stdin"
                    sh "docker tag ${IMAGE_NAME}:latest ${IMAGE_NAME}:v1.0.${BUILD_NUMBER}"
                    sh "docker push ${IMAGE_NAME}:latest"
                    sh "docker push ${IMAGE_NAME}:v1.0.${BUILD_NUMBER}"
                }
            }
        }

        stage('Clean up') {
            steps {
                sh "docker rmi ${IMAGE_NAME}:latest || true"
                sh "docker rmi ${IMAGE_NAME}:v1.0.${BUILD_NUMBER} || true"
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline succeeded. Image pushed to Docker Hub: ${IMAGE_NAME}"
        }
        failure {
            echo "❌ Pipeline failed."
        }
    }
}
