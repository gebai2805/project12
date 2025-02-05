pipeline {
    agent any
    tools{
        maven 'maven'
    }
    environment {
        GIT_REPO = 'https://github.com/gebai2805/DevOps_Project.git' 
        BRANCH = 'main'  
        IMAGE_NAME = 'your-dockerhub-username/your-image-name'  // Change this
        DOCKER_CREDENTIALS = 'docker-hub-credentials'  // Jenkins credential ID for Docker Hub
        CONTAINER_NAME = 'java-web-app'  // Change if needed
    }
    stages {
        stage('Checkout Code') {
            steps {
                git branch: "${BRANCH}", url: "${GIT_REPO}"
            }
        }
        stage ('Build')
        {
          steps {
                sh 'mvn clean package'
          }  
        }
        stage ('Build docker image')
        {
          steps {
            script {
                  sh "docker build -t ${IMAGE_NAME}:latest ."
            }
          }  
        }
        stage ('Push to dockerhub')
        {
          steps {
                withDockerRegistry([credentialsId: "${DOCKER_CREDENTIALS}", url: ""]) {
                    sh "docker push ${IMAGE_NAME}:latest"
          }  
        }
        stage ('Remove docker image from local host')
        {
          steps {
            script {
                    sh "docker rmi ${IMAGE_NAME}:latest"
          }  
        }
        stage ('Run docker container')
        {
          steps {
            script {
                    // Stop and remove existing container if it exists
                    sh """
                    docker stop ${CONTAINER_NAME} || true
                    docker rm ${CONTAINER_NAME} || true
                    """

                    // Run a new container from the pushed image
                    sh "docker run -d --name ${CONTAINER_NAME} -p 8080:8080 ${IMAGE_NAME}:latest"
            }
          }  
        }
        }
        Post
        {
          always {
            echo "Deployment Successful"
          }  
          success{
            echo "Success"
          }
          failure {

          }
        }
    }
}
