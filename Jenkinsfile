def CURR_STAGE="Start"
pipeline {
    agent {
        label 'build-test'
    }
    environment {
        // DOCKERHUB_CREDENTIALS = credentials('dockerhub')
    }
    stages {
        stage('Git') {
            steps {
                CURR_STAGE="Git"
                checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[credentialsId: 'github-repo', url: 'https://github.com/amihaiba/elta-project.git']])
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
