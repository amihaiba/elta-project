CURR_STAGE="Start"
pipeline {
    agent {
        kubernetes {
            yamlFile 'build-agent.yaml'
            defaultContainer 'alpine'
            idleMinutes 1
        }
    }
    environment {
        DOCKERHUB_CREDENTIALS = credentials('docker-cred')
        DOCKER_IMAGE_NAME = "amihaiba/eltamvc"
    }
    stages {
        // Clean the project's workspace
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
                    CURR_STAGE="Git checkout"
                }
                git branch: 'main', credentialsId: 'git-cred', url: 'https://github.com/amihaiba/elta-project.git'
            }
        }
        // Build the docker image using a multistage Dockerfile
        stage('Build') {
            steps {
                script {
                    CURR_STAGE="Build"
                    // GIT_COMMIT_REV = sh (script: 'git log -n 1 --pretty=format:%h', returnStdout: true)
                    // appImage = docker.build("$DOCKER_IMAGE_NAME}:0.1.0-${GIT_COMMIT_REV}")
                }
                container('alpine') {
                    sh 'uptime'
                }
            }
        }
        stage('Delivery') {
            steps {
                script {
                    CURR_STAGE="Delivery"
                    docker.withRegistry('https://registry.hub.docker.com', 'docker-cred') {
                        appImage.push("0.1.${BUILD_NUMBER}-${GIT_COMMIT_REV}")
                        appImage.push("latest")
                    }
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
            echo "Build ${BUILD_NUMBER} completed successfuly"
        }
        failure {
            echo "Build ${BUILD_NUMBER} failed at ${CURR_STAGE} stage"
        }
        always {
            sh 'docker logout'
        }
    }
}
