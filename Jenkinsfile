def CURR_STAGE="Start"
pipeline {
    agent any
    // agent {
    //     label 'build-test'
    // }
    environment {
        // DOCKERHUB_CREDENTIALS = credentials('dockerhub')
    }
    stages {
        stage('Git') {
            steps {
                script {
                    CURR_STAGE="Git"
                }
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'github-repo', url: 'https://github.com/amihaiba/elta-project.git']])
            }
        }
        stage('Build') {
            steps {
                CURR_STAGE="Build"
            }
        }
        stage('Delivery') {
            steps {
                CURR_STAGE="Delivery"
            }
        }
        stage('Deployment') {
            steps {
                CURR_STAGE="Deployment"
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
