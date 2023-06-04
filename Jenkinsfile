CURR_STAGE = "Start"
pipeline {
    agent {
        kubernetes {
            yamlFile './kubernetes/build-agent.yaml'
            defaultContainer 'builder'
            idleMinutes 1
        }
    }
    environment {
        IMAGE_NAME = "amihaiba/eltamvc"
        IMAGE_VERSION = "0.1.0"
    }
    stages {
        // Clean the project's workspace (No need since containers are ephemeral)
        stage('Test') {
            agent {
                kubernetes {
                    yamlFile './kubernetes/deploy-agent.yaml'
                    defaultContainer 'deployer'
                    idleMinutes 1
                }
            }
            steps {
                withKubeConfig([serverUrl: 'https://kubernetes.default.svc']) {
                    container('deployer') {
                        sh 'kubectl get pods'
                    }
                }
            }
        }
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
                }
                git branch: 'main', credentialsId: 'git-cred', url: 'https://github.com/amihaiba/elta-project.git'
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
                        sh "docker build -t ${IMAGE_NAME}:${IMAGE_VERSION}-${GIT_COMMIT[0..6]}-jenkins ${WORKSPACE}"
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
                    sh "docker push ${IMAGE_NAME}:${IMAGE_VERSION}-${GIT_COMMIT[0..6]}-jenkins"
                }
            }
        }
        stage('Deployment') {
            steps {
                agent {
                    kubernetes {
                        yamlFile './kubernetes/deploy-agent.yaml'
                        defaultContainer 'deployer'
                        idleMinutes 1
                    }
                }
                script {
                    CURR_STAGE = "Deployment"
                }
            }
        }
    }
    post {
        success {
            echo "Build number ${BUILD_DISPLAY_NAME} completed successfuly"
        }
        failure {
            echo "Build number ${BUILD_DISPLAY_NAME} failed at ${STAGE_NAME} stage"
        }
        always {
            sh 'docker logout'
        }
    }
}
