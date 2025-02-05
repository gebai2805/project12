pipeline {
    agent any

    tools{
        maven 'maven'
    }

    stages{
        stage('Check and remove container'){
            steps{
                script{
                    def containerExists = sh(script: "docker ps -q -f name=geetha", returnStdout: true).trim()
                    if (containerExists) {
                    sh "docker stop geetha"
                    sh "docker rm geetha"
                    }
                }
            }
        }
        stage('Build package'){
            steps{
                sh 'mvn clean package'
            }
        }
        stage('Create image'){
            steps{
                sh 'sudo docker build -t app /var/lib/jenkins/workspace/Zoo/'
            }
        }
        stage('Assign tag'){
            steps{
                sh 'docker tag app gebai2805/app'
            }
        }
        stage('Push to dockerhub'){
            steps{
                sh 'echo "Edml@080697" | docker login -u "gebai2805" --password-stdin'
                sh 'docker push gebai2805/app'
            }
        }
        stage('Remove images'){
            steps{
                sh 'docker rmi -f $(docker images -q)'
            }
        }
        stage('Pull image from DockerHub'){
            steps{
                sh 'docker pull gebai2805/app'
            }
        }
        stage('Run a container'){
            steps{
                sh 'docker run -it -d --name geetha -p 8081:8080 gebai2805/app'
            }
        }
    }
    post {
        success {
            echo 'Deployment successful'
        }
        failure {
            sh 'docker rm -f geetha'
        }
        always{
            echo 'Deployed'
        }
    }

}