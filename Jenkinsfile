pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "${env.BRANCH_NAME == 'main' ? 'nodemain:v1.0' : 'nodedev:v1.0'}"
        CONTAINER_PORT = "${env.BRANCH_NAME == 'main' ? '3000' : '3001'}"
        HOST_PORT = "${env.BRANCH_NAME == 'main' ? '3000' : '3001'}"
    }

    tools {
        nodejs "node" // Name you used in Global Tool Configuration
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'npm test'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t $DOCKER_IMAGE ."
            }
        }

        stage('Stop and Remove Previous Containers') {
            steps {
                sh """
                    docker ps -q --filter "ancestor=$DOCKER_IMAGE" | xargs -r docker stop
                    docker ps -a -q --filter "ancestor=$DOCKER_IMAGE" | xargs -r docker rm
                """
            }
        }

        stage('Run Docker Container') {
            steps {
                sh "docker run -d -p ${HOST_PORT}:3000 $DOCKER_IMAGE"
            }
        }
    }
}
