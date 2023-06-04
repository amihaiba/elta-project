CURR_STAGE = "Start"
GIT_COMMIT = ""
pipeline {
    agent {
        kubernetes {
            // yamlFile './kubernetes/kaniko-agent.yaml'
            yamlFile './kubernetes/build-agent.yaml'
            defaultContainer 'builder'
            idleMinutes 2
        }
    }
    environment {
        IMAGE_NAME = "amihaiba/eltamvc"
        IMAGE_MAJOR = 0
        IMAGE_MINOR = 1
    }
    stages {
        // Clean the project's workspace (No need since containers are ephemeral)
        stage('Clean') {
            steps {
                script {
                    CURR_STAGE="Clean"
                }
                cleanWs()
            }
        }

        // Fetch source files from the github repository
        stage('Git checkout') {
            steps {
                script {
                    CURR_STAGE = "Git checkout"
                    GIT_COMMIT = sh 'git logs -n 1 --pretty=format"%h"'
                }
                git branch: 'main', credentialsId: 'git-cred', url: 'https://github.com/amihaiba/elta-project.git'
                container('builder') {
                    echo ${GIT_COMMIT}
                }
            }
        }
        // Build the docker image using a multistage Dockerfile
        stage('Build and deliver') {
            steps {
                script {
                    CURR_STAGE="Build"
                }
                withCredentials([usernamePassword(credentialsId: 'docker-cred', usernameVariable: 'USR', passwordVariable: 'PWD')]) {
                    container('builder') {
                        sh "echo ${PWD} | docker login -u ${USR} --password-stdin"
                        sh "docker build -t ${IMAGE_NAME}:${IMAGE_MAJOR}.${IMAGE_MINOR}.0-jenkins ${WORKSPACE}"
                    }
                }
            }
        }
        stage('Delivery') {
            steps {
                script {
                    CURR_STAGE="Delivery"
                }
                container('builder') {
                    sh "docker push ${IMAGE_NAME}:0.1.0-jenkins"
                }
            }
        }
        stage('Deployment') {
            steps {
                script {
                    CURR_STAGE="Deployment"
                }
            }
        }
    }
    post {
        success {
            echo "Build number ${BUILD_DISPLAY_NAME} completed successfuly"
        }
        failure {
            echo "Build number ${BUILD_DISPLAY_NAME} failed at ${CURR_STAGE} stage"
        }
        always {
            sh 'docker logout'
        }
    }
}
