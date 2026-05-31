pipeline {
    agent any
    environment {
        SERVICE = 'gateway'
        NAME = "rmcardosoo/${SERVICE}"
    }
    stages {
        stage('Build') {
            steps {
                sh 'mvn -B -DskipTests clean package'
            }
        }
        stage('Build & Push Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-credential',
                    usernameVariable: 'USERNAME',
                    passwordVariable: 'TOKEN')]) {
                    sh "docker login -u $USERNAME -p $TOKEN"
                    sh "docker buildx create --use --platform=linux/arm64,linux/amd64 --node multi-platform-builder-${SERVICE} --name multi-platform-builder-${SERVICE}"
                    sh "docker buildx build --platform=linux/arm64,linux/amd64 --push --tag ${NAME}:latest --tag ${NAME}:${BUILD_ID} -f Dockerfile ."
                    sh "docker buildx rm --force multi-platform-builder-${SERVICE}"
                }
            }
        }
    }
}