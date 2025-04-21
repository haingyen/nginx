pipeline {
    agent {
        label "ec2-agent"
    }
    
    environment {
        DOCKER_HUB_USER = "haingyen"
        DOCKER_HUB_REPO = "${DOCKER_HUB_USER}/myrepo"
        DOCKER_IMAGE_TAG = "4.0.0"
    }
    
    stages {
        // stage('Checkout') {
        //     steps {
        //         git branch: 'main', url: 'https://github.com/haingyen/nginx.git'
        //     }
        // }
        
        stage('Build Docker Image') {
            steps {
                script {
                    // Build image từ Dockerfile
                    sh "sudo docker build -t ${DOCKER_HUB_REPO}:${DOCKER_IMAGE_TAG} ."
                    
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
    
    // post {
    //     // always {
    //     //     // Clean up workspace
    //     //     deleteDir()
            
    //     //     // Xóa image local để tiết kiệm dung lượng
    //     //     script {
    //     //         try {
    //     //             sh "docker rmi ${DOCKER_HUB_REPO}:${DOCKER_IMAGE_TAG}"
    //     //             if (env.BRANCH_NAME == 'main' || env.BRANCH_NAME == 'master') {
    //     //                 sh "docker rmi ${DOCKER_HUB_REPO}:${env.BUILD_NUMBER}"
    //     //             }
    //     //         } catch (e) {
    //     //             echo "Failed to remove images: ${e}"
    //     //         }
    //     //     }
    //     // }
    //     success {
    //         slackSend(
    //             channel: '#deployments',
    //             message: "Successfully pushed ${DOCKER_HUB_REPO}:${DOCKER_IMAGE_TAG} to Docker Hub"
    //         )
    //     }
    //     failure {
    //         slackSend(
    //             channel: '#alerts',
    //             message: "Failed to push Docker image. Build: ${env.BUILD_URL}"
    //         )
    //     }
    // }
}