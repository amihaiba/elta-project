CURR_STAGE="Start"
pipeline {
    agent {
        kubernetes {
            yamlFile './kubernetes/kaniko-agent.yaml'
            defaultContainer 'kaniko'
            idleMinutes 2
        }
    }
    environment {
        DOCKERHUB_CREDENTIALS = credentials('docker-cred')
        DOCKER_IMAGE_NAME = "amihaiba/eltamvc"
    }
    stages {
        // Clean the project's workspace (No need since containers are ephemeral)
        // stage('Clean') {
        //     steps {
        //         script {
        //             CURR_STAGE="Clean"
        //         }
        //         cleanWs()
        //     }
        // }

        Fetch source files from the github repository
        stage('Git checkout') {
            steps {
                script {
                    CURR_STAGE="Git checkout"
                }
                git branch: 'main', credentialsId: 'git-cred', url: 'https://github.com/amihaiba/elta-project.git'
            }
        }
        // Build the docker image using a multistage Dockerfile
        stage('Build and deliver') {
            steps {
                script {
                    CURR_STAGE="Build"
                }
                container('kaniko') {
                    // sh "docker build -t amihaiba/eltamvc:0.1.0-jenkins /home/jenkins/agent/workspace/elta-pipeline/eltaMVC"
                    sh "/kaniko/executor --context=dir:///home/jenkins/agent/workspace/elta-pipeline --destination=amihaiba/eltamvc:0.1.0-jenkins"
                    // sh "/kaniko/executor --context=git://github.com/amihaiba/elta-project --destination=amihaiba/eltamvc:0.1.0-jenkins"
                }
            }
        }
        // stage('Delivery') {
        //     steps {
        //         script {
        //             CURR_STAGE="Delivery"
        //         }
        //         container('builder') {
        //             // sh "echo ${DOCKERHUB_PASS} | docker login -u ${DOCKERHUB_USER}"
        //             // sh "docker push amihaiba/eltamvc:0.1.0-jenkins"
        //         }
        //     }
        // }
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
        // always {
        //     sh 'docker logout'
        // }
    }
}
