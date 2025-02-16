pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                script {
                    checkout scm
                    echo "Building from branch: ${env.BRANCH_NAME}"
                }
            }
        }

        stage('Install & Test') {
            steps {
                script {
                    sh 'npm install'
                    sh 'npm test'  // or any test command
                }
            }
        }

        stage('Build Docker Image') {
            when {
                expression { return fileExists('Dockerfile') }
            }
            steps {
                script {
                    // Optional: build a Docker image
                    def imageTag = env.BRANCH_NAME == 'main' ? 'nodemain:v1.0' : 'nodedev:v1.0'
                    sh "docker build -t ${imageTag} ."
                    echo "Docker image ${imageTag} built."
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    sh 'docker ps -q --filter "name=nodeapp" | xargs -r docker stop || true'
                    sh 'docker ps -aq --filter "name=nodeapp" | xargs -r docker rm || true'

                    def port = env.BRANCH_NAME == 'main' ? '3000' : '3001'
                    def imageTag = env.BRANCH_NAME == 'main' ? 'nodemain:v1.0' : 'nodedev:v1.0'

                    echo "Deploying on port: ${port}"
                    sh "docker run -d -p ${port}:${port} --name nodeapp -e PORT=${port} ${imageTag}"
                }
            }
        }
    }
}
