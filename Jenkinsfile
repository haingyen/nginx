pipeline {
    agent any
    
    environment {
        DOCKER_HUB_USER = "haingyen"
        DOCKER_HUB_REPO = "${DOCKER_HUB_USER}/myrepo"
        DOCKER_IMAGE_TAG = "latest"
    }
    
    stages {
       
        stage('Build Docker Image') {
            steps {
                script {
                    // Build image từ Dockerfile
                    sh "docker build -t ${DOCKER_HUB_REPO}:${DOCKER_IMAGE_TAG} ."
                    
                    // Tag thêm nếu cần
                    // sh "docker tag ${DOCKER_HUB_REPO}:${DOCKER_IMAGE_TAG} ${DOCKER_HUB_REPO}:${env.BUILD_NUMBER}"
                }
            }
        }
        
        stage('Login to Docker Hub') {
            steps {
                withCredentials([string(credentialsId: 'docker-hub-creds', variable: 'DOCKER_HUB_TOKEN')]) {
                    sh """
                        # Đăng nhập Docker Hub bằng token
                        echo \$DOCKER_HUB_TOKEN | docker login -u ${DOCKER_HUB_USER} --password-stdin
                    """
                }
            }
        }
        
        stage('Push Image to Docker Hub') {
            steps {
                script {
                    // Push image chính
                    sh "docker push ${DOCKER_HUB_REPO}:${DOCKER_IMAGE_TAG}"
                    
                    // Push thêm tag nếu cần
                    if (env.BRANCH_NAME == 'main' || env.BRANCH_NAME == 'master') {
                        sh "docker push ${DOCKER_HUB_REPO}:${env.BUILD_NUMBER}"
                    }
                }
            }
        }
        
        stage('Logout from Docker Hub') {
            steps {
                sh "docker logout"
            }
        }
    }
}