pipeline {
    agent any

    environment {
        PORT = "${env.BRANCH_NAME == 'main' ? '3000' : '3001'}"
        IMAGE_NAME = "${env.BRANCH_NAME == 'main' ? 'nodemain:v1.0' : 'nodedev:v1.0'}"
        CONTAINER_NAME = "${env.BRANCH_NAME == 'main' ? 'nodeapp_main_container' : 'nodeapp_dev_container'}"
        DOCKER_HUB_REPO = "tahabarann/cicd-pipeline-task"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: env.BRANCH_NAME, url: 'https://github.com/0x421F/cicd-pipeline-task.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t $DOCKER_HUB_REPO:$IMAGE_NAME ."
            }
        }

        stage('Push Docker Image to Docker Hub') {
            steps {
                withDockerRegistry([credentialsId: '78788950-dd63-4609-b016-7592ffdd2a01', url: '']) {
                    sh "docker push $DOCKER_HUB_REPO:$IMAGE_NAME"
                }
            }
        }

        stage('Trigger Deployment Pipeline') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'main') {
                        build job: 'Deploy_to_main'
                    } else if (env.BRANCH_NAME == 'dev') {
                        build job: 'Deploy_to_dev'
                    }
                }
            }
        }
    }

    post {
        success {
            echo "Successfully built and pushed Docker image!"
        }
        failure {
            echo "Pipeline failed!"
        }
    }
}
