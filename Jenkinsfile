def CURR_STAGE="Start"
pipeline {
    agent any
    // agent {
    //     label 'build-test'
    // }
    // environment {
    //     DOCKERHUB_CREDENTIALS = credentials('dockerhub')
    // }
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
                script {
                    CURR_STAGE="Build"
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
            echo "Build ${env.BUILD_NUMBER} completed successfuly"
        }
        failure {
            echo "Build ${env.BUILD_NUMBER} failed at ${CURR_STAGE}"
        }
        always {
            sh 'docker logout'
        }
    }
}
