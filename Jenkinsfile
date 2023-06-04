CURR_STAGE = "Start"
pipeline {
    agent {
        kubernetes {
            yamlFile './kubernetes/build-agent.yaml'
            defaultContainer 'builder'
            idleMinutes 2
        }
    }
    environment {
        IMAGE_NAME = "amihaiba/eltamvc"
        IMAGE_VERSION = "0.1.0"
    }
    stages {
        // Clean the project's workspace (No need since containers are ephemeral)
        stage('Clean') {
            steps {
                script {
                    CURR_STAGE="Clean"
                }
                cleanWs()
                // Clean up old images
                // sh 'docker images | grep " [days|months|weeks|years]* ago" | awk "{print $3 is $4 $5 old}"'
                // sh 'docker images'
            }
        }

        // Fetch source files from the github repository
        stage('Git checkout') {
            steps {
                script {
                    CURR_STAGE = "Git checkout"
                }
                // git branch: 'main', credentialsId: 'git-cred', url: 'https://github.com/amihaiba/elta-project.git'
            }
        }
        // Build the docker image using a multistage Dockerfile
        stage('Build') {
            steps {
                script {
                    CURR_STAGE="Build"
                }
                withCredentials([usernamePassword(credentialsId: 'docker-cred', usernameVariable: 'USR', passwordVariable: 'PWD')]) {
                    container('builder') {
                        sh "echo ${PWD} | docker login -u ${USR} --password-stdin"
                        // sh "docker build -t ${IMAGE_NAME}:${IMAGE_VERSION}-${GIT_COMMIT[0..6]}-jenkins ${WORKSPACE}"
                    }
                }
            }
        }
        stage('Delivery') {
            steps {
                script {
                    CURR_STAGE="Delivery"
                }
                // container('builder') {
                //     sh "docker push ${IMAGE_NAME}:${IMAGE_VERSION}-${GIT_COMMIT[0..6]}-jenkins"
                // }
            }
        }
        stage('Deployment') {
            steps {
                script {
                    CURR_STAGE="Deployment"
                }
                withKubeConfig([]) { //serverUrl: '192.168.49.2:32000'
                    sh 'kubectl get pods'
                }
                // container('builder') {
                //     sh 'kubectl get pods'
                // }
                // kubernetesDeploy(configs: "kubernetes/eltamvc.yaml")
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
