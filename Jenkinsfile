pipeline {
    agent any

    environment {
        DOCKER_IMAGE_NAME = 'musicman123/music_man_docker_repo'       

        DOCKER_IMAGE_TAG = 'nginx_image_with_hello_world'

        EC2_SERVER = '3.76.209.132' 

        DOCKERHUB_USERNAME = credentials('dockerhub_username')      
        
        DOCKERHUB_PASS = credentials('dockerhub_pass') 
    }

    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t $DOCKER_IMAGE_NAME:$DOCKER_IMAGE_TAG .'
                }
            }
        }

        stage('Push Docker Image to Docker Hub') {
            steps {
                script {
                    sh 'docker login -u $DOCKERHUB_USERNAME -p $DOCKERHUB_PASS'

                    sh 'docker push $DOCKER_IMAGE_NAME:$DOCKER_IMAGE_TAG'
                }
            }
        }

        stage('Deploy to EC2') {
            steps {
                sshagent(['SSH-AWS-EC2-Access']) {
                    sh 'ssh -o StrictHostKeyChecking=no ubuntu@$EC2_SERVER "docker login -u $DOCKERHUB_USERNAME -p $DOCKERHUB_PASS && docker stop $DOCKER_IMAGE_TAG || true && docker rm $DOCKER_IMAGE_TAG || true && docker rmi $DOCKER_IMAGE_NAME:$DOCKER_IMAGE_TAG || true && docker run --name DOCKER_IMAGE_TAG --restart unless-stopped -d -p 80:80 $DOCKER_IMAGE_NAME:$DOCKER_IMAGE_TAG"'
                }
            }
        }       
    } 
}
