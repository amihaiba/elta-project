pipeline {
    agent {
        label 'build-test'
    }
    // environment {
    //     DOCKERHUB_CREDENTIALS = credentials('dockerhub')
    // }
    stages {
        stage('Git fetch') {
            steps {
                checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[credentialsId: 'github-repo', url: 'https://github.com/amihaiba/elta-project.git']])
            }
    }
    }
}
