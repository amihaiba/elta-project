CURR_STAGE="Start"
pipeline {
    agent any
    // agent {
    //     label 'build-test'
    // }
    environment {
        DOCKERHUB_CREDENTIALS = credentials('docker-cred')
        DOCKER_IMAGE_NAME = "amihaiba/eltamvc"
    }
    stages {
        stage('Clean') {
            steps {
                script {
                    CURR_STAGE="Clean"
                }
                cleanWs()
            }
        }
        stage('Git checkout') {
            steps {
                script {
                    CURR_STAGE="Git checkout"
                }
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'github-repo', url: 'https://github.com/amihaiba/elta-project.git']])
            }
        }
        stage('Build') {
            steps {
                // withCredentials([
                //     usernamePassword(credentials: 'docker-cred', usernameVariable: USER, passwordVariable: PASSWD)
                // ]) {}
                script {
                    CURR_STAGE="Build"
                    GIT_COMMIT_REV = sh (script: 'git log -n 1 --pretty=format:"h"', returnStdout: true)
                    appImage = docker.build("$DOCKER_IMAGE_NAME}:0.1.0-${GIT_COMMIT_REV}")
                    appImage.push()
                }
            }
        }
        stage('Delivery') {
            steps {
                script {
                    CURR_STAGE="Delivery"
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
            echo "Build ${BUILD_NUMBER} failed at ${CURR_STAGE}"
        }
        always {
            sh 'docker logout'
        }
    }
}
