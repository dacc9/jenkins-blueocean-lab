pipeline {
    agent any
 
    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials')
        DOCKERHUB_REPO        = "${DOCKERHUB_CREDENTIALS_USR}/jenkins-blueocean-lab"
        IMAGE_TAG             = "${env.BUILD_NUMBER}"
    }
 
    stages {
        stage('Clone repository') {
            steps {
                echo "Repository cloned by Jenkins SCM checkout."
                sh 'ls -la'
            }
        }
 
        stage('Build image') {
            steps {
                sh """
                    docker build -t ${DOCKERHUB_REPO}:${IMAGE_TAG} .
                    docker tag  ${DOCKERHUB_REPO}:${IMAGE_TAG} ${DOCKERHUB_REPO}:latest
                """
            }
        }
 
        stage('Push image') {
            steps {
                sh """
                    echo "${DOCKERHUB_CREDENTIALS_PSW}" | \
                        docker login -u "${DOCKERHUB_CREDENTIALS_USR}" --password-stdin
                    docker push ${DOCKERHUB_REPO}:${IMAGE_TAG}
                    docker push ${DOCKERHUB_REPO}:latest
                    docker logout
                """
            }
        }
    }
 
    post {
        always {
            sh 'docker rmi ${DOCKERHUB_REPO}:${IMAGE_TAG} ${DOCKERHUB_REPO}:latest || true'
        }
        success { echo "Pipeline completed successfully. Image pushed to Docker Hub." }
        failure { echo "Pipeline failed. Check logs above." }
    }
}
