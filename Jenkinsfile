pipeline {
    agent any

    environment {
        // Set PORT and IMAGE_NAME based on the branch name
        PORT = "${env.BRANCH_NAME == 'main' ? '3000' : '3001'}"
        IMAGE_NAME = "${env.BRANCH_NAME == 'main' ? 'nodeapp_main:v1.0' : 'nodeapp_dev:v1.0'}"
        CONTAINER_NAME = "${env.BRANCH_NAME == 'main' ? 'nodeapp_main_container' : 'nodeapp_dev_container'}"
    }

    stages {
        stage('Checkout') {
            steps {
                // Clone the specific branch
                git branch: env.BRANCH_NAME, url: 'https://github.com/0x421F/cicd-pipeline-task.git'
            }
        }

        stage('Update Logo') {
            steps {
                script {
                    // Define the path to the logo based on the branch
                    def logoPath = env.BRANCH_NAME == 'main' ? 'assets/logo_main.svg' : 'assets/logo_dev.svg'
                    // Copy the appropriate logo to the public directory
                    sh "cp ${logoPath} public/logo.svg"
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                // Install Node.js dependencies
                sh 'npm install'
            }
        }

        stage('Run Tests') {
            steps {
                // Run tests (assuming you have a test script defined in package.json)
                sh 'npm test'
            }
        }

        stage('Build Docker Image') {
            steps {
                // Build the Docker image with the appropriate tag
                sh "docker build -t $IMAGE_NAME ."
            }
        }

        stage('Deploy') {
            steps {
                script {
                    // Stop and remove the existing container if it exists
                    sh """
                    if [ \$(docker ps -q -f name=$CONTAINER_NAME) ]; then
                        docker stop $CONTAINER_NAME
                        docker rm $CONTAINER_NAME
                    fi
                    """
                    // Run the new container
                    sh "docker run -d -e PORT=$PORT -p $PORT:$PORT --name $CONTAINER_NAME $IMAGE_NAME"
                }
            }
        }
    }

    post {
        success {
            echo "Deployment successful: Application is running on http://localhost:$PORT"
        }
        failure {
            echo "Deployment failed. Please check the logs for more details."
        }
    }
}

