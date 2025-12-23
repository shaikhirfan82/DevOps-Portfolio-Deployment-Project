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
            steps {
                unstash 'code'

                withCredentials([string(credentialsId: 'dockerhub-pass', variable: 'DOCKERHUB_PASS')]) {
                    sh '''
                    docker login -u $DOCKERHUB_USER -p $DOCKERHUB_PASS
                    docker build -t $DOCKERHUB_USER/$IMAGE_NAME:latest .
                    docker push $DOCKERHUB_USER/$IMAGE_NAME:latest
                    '''
                }
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
