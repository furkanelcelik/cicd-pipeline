pipeline {
    agent any

    tools {
        nodejs 'node'
    }

    environment {
        APP_NAME = "my-node-app"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Set Environment & Logo') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'main') {
                        echo "BRANCH: main. Setting port to 3000."
                        env.PORT = '3000'
                        sh 'cp src/logo-main.svg src/logo.svg'
                    } else if (env.BRANCH_NAME == 'dev') {
                        echo "BRANCH: dev. Setting port to 3001."
                        env.PORT = '3001'
                        sh 'cp src/logo-dev.svg src/logo.svg'
                    }
                    env.DOCKER_TAG = "${env.APP_NAME}-${env.BRANCH_NAME}:v1.0"
                }
            }
        }

        stage('Build Application') {
            steps {
                dir('app') {
                   sh 'npm install'
                }
            }
        }

        stage('Test') {
            steps {
                dir('app') {
                    sh 'npm test'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${env.DOCKER_TAG} ."
            }
        }

        stage('Deploy') {
            steps {
                script {
                    def containerName = "${env.APP_NAME}-${env.BRANCH_NAME}"
                    sh "docker stop ${containerName} || true"
                    sh "docker rm ${containerName} || true"
                    sh "docker run -d --name ${containerName} -p ${env.PORT}:3000 ${env.DOCKER_TAG}"
                }
            }
        }
    }
}

