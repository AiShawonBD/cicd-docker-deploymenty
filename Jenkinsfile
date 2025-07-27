pipeline {
    agent any

    environment {
        IMAGE_NAME = 'aishawon/cicd_docker_demo'
        IMAGE_TAG = "${IMAGE_NAME}:${env.GIT_COMMIT}"
    }

    stages {
        stage('Setup Python Env') {
            steps {
                sh '''
                    python3 -m venv venv
                    . venv/bin/activate
                    pip install --upgrade pip
                    pip install -r requirements.txt
                '''
            }
        }

        stage('Run Tests') {
            steps {
                sh '''
                    . venv/bin/activate
                    pytest
                '''
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-creds', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh 'echo ${PASSWORD} | docker login -u ${USERNAME} --password-stdin'
                }
                echo '✅ Docker Hub login successful'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                    docker build -t ${IMAGE_TAG} .
                    docker images
                '''
                echo '✅ Docker image built successfully'
            }
        }

        stage('Push Docker Image') {
            steps {
                sh 'docker push ${IMAGE_TAG}'
                echo '✅ Docker image pushed successfully'
            }
        }
    }
}
