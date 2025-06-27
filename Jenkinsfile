pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'shirisha2018/amazonlinux-nginx'
        TAG = 'v0.0.1'
    }

    tools {
        maven 'maven3'       // Configure Maven in Jenkins Global Tools
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/Shirisha95-palla/multi-stage-example.git', branch: 'master'
            }
        }

        stage('Build with Maven') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh """
                docker build -t ${DOCKER_IMAGE}:${TAG} .
                """
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh """
                    echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                    docker push ${DOCKER_IMAGE}:${TAG}
                    docker logout
                    """
                }
            }
        }
    }

    post {
        success {
            echo "✅ Image pushed: ${DOCKER_IMAGE}:${TAG}"
        }
        failure {
            echo "❌ Build failed."
        }
    }
}
