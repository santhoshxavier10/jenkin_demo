pipeline {
    agent any 

    options {
        skipDefaultCheckout(true)   // prevent system-generated checkout
        timestamps()                // add timestamps in logs
    }

    environment {
        DOCKERHUB_CREDENTIALS = credentials('testdock1')  
        IMAGE_NAME = "nemunis/flask"
        CONTAINER_NAME = "myapp-container"
    }

    stages {
        stage('Checkout Code') {
            steps {
                echo "Checking out repository..."
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {  
                script {
                    def start = System.currentTimeMillis()
                    echo "Building Docker image..."
                    sh "docker build -t $IMAGE_NAME:$BUILD_NUMBER ."
                    def end = System.currentTimeMillis()
                    echo "⏱ Docker build took $((end - start) / 1000) seconds"
                }
            }
        }

        stage('Login to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'testdock1', usernameVariable: 'DOCKERHUB_USERNAME', passwordVariable: 'DOCKERHUB_PASSWORD')]) {
                    echo "Logging in to Docker Hub..."
                    sh "echo $DOCKERHUB_PASSWORD | docker login -u $DOCKERHUB_USERNAME --password-stdin"
                }
            }
        }

        stage('Push Image to DockerHub') {
            steps {
                script {
                    def start = System.currentTimeMillis()
                    echo "Pushing Docker image to Docker Hub..."
                    sh "docker push $IMAGE_NAME:$BUILD_NUMBER"
                    sh "docker tag $IMAGE_NAME:$BUILD_NUMBER $IMAGE_NAME:latest"
                    sh "docker push $IMAGE_NAME:latest"
                    def end = System.currentTimeMillis()
                    echo "⏱ Docker push took $((end - start) / 1000) seconds"
                }
            }
        }

        stage('Deploy to Staging') {
            steps {
                echo "Deploying container on EC2..."
                sh "docker pull $IMAGE_NAME:$BUILD_NUMBER"
                sh "docker stop $CONTAINER_NAME || true"
                sh "docker rm $CONTAINER_NAME || true"
                sh "docker run -d --name $CONTAINER_NAME -p 8000:8000 $IMAGE_NAME:$BUILD_NUMBER"
            }
        }
    }

    post {
        always {
            echo "Cleaning up..."
            sh 'docker logout'
        }
    }
}
