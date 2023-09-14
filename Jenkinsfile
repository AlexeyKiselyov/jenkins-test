pipeline {
    agent any

    environment {
        // Определите имя образа Docker, которое вы хотите использовать
        DOCKER_IMAGE_NAME = 'musicman123/music_man_docker_repo'       

        // Определите тег для образа
        DOCKER_IMAGE_TAG = 'nginx_image_with_hello_world'

        // Определите адрес EC2-сервера
        EC2_SERVER = '3.76.209.132' 

        DOCKERHUB_USERNAME = 'musicman123'      
        
        DOCKERHUB_PASS = 'fender123' 
    }

    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    // Выполните команду по созданию Docker-образа
                    sh "docker build -t ${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG} ."
                }
            }
        }

        stage('Push Docker Image to Docker Hub') {
            steps {
                script {
                    // Войдите в Docker Hub с вашими учетными данными
                    sh "docker login -u ${DOCKERHUB_USERNAME} -p ${DOCKERHUB_PASS}"

                    // Отправьте образ на Docker Hub
                    sh "docker push ${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG}"
                }
            }
        }

        stage('Deploy to EC2') {
            steps {
                script {
                    def remoteServer = [:]
                    remoteServer.name = 'RemoteServer'
                    remoteServer.host = '${EC2_SERVER}'
                    remoteServer.user = 'ubuntu'
                    remoteServer.credentialsId = 'SSH-AWS-EC2-Access'

                    sshCommand remote: remoteServer, command: 'docker login -u ${DOCKERHUB_USERNAME} -p ${DOCKERHUB_PASS} && docker stop ${DOCKER_IMAGE_TAG};docker rm ${DOCKER_IMAGE_TAG} || true && docker rmi ${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG} || true && docker run --name ${DOCKER_IMAGE_TAG} --restart unless-stopped -d -p 80:80 ${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG}'
                }
            }
        }
    } 
}