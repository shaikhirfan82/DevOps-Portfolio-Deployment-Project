pipeline {
    agent none

    environment {
        DOCKERHUB_USER = 'shaikhirfan82'
        IMAGE_NAME = 'portfolio'
    }

    stages {

        stage('Checkout Code') {
            agent any
            steps {
                git url: 'https://github.com/shaikhirfan82/DevOps-Portfolio-Deployment-Project.git'
                stash name: 'code', includes: '**'
            }
        }

        stage('Build & Push Image') {
            agent { label 'build-node' }
            environment {
                DOCKERHUB_PASS = credentials('dockerhub-pass')
            }
            steps {
                unstash 'code'
                sh '''
                echo "$DOCKERHUB_PASS" | docker login -u $DOCKERHUB_USER --password-stdin
                docker build -t $DOCKERHUB_USER/$IMAGE_NAME:latest .
                docker push $DOCKERHUB_USER/$IMAGE_NAME:latest
                '''
            }
        }

        stage('Deploy') {
            agent { label 'deploy-node' }
            steps {
                sh '''
                docker pull $DOCKERHUB_USER/$IMAGE_NAME:latest
                docker stop portfolio || true
                docker rm portfolio || true
                docker run -d -p 80:80 --name portfolio $DOCKERHUB_USER/$IMAGE_NAME:latest
                '''
            }
        }
    }
}
