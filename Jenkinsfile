pipeline {
    agent any

    options {
        timestamps()
        disableConcurrentBuilds()
        buildDiscarder(logRotator(numToKeepStr: '10'))
    }

    environment {
        DOCKERHUB_USER = "lakshvar96"
        frontend_image = "thinkify-front"
        backend_image = "thinkify-back"
        GIT_REPO = "https://github.com/Lakshmanan1996/Thinkify.git"
    }

    stages {

        /* ===================== CHECKOUT ===================== */
        stage('Checkout Code') {
            
            steps {
                git branch: 'master', url: "${GIT_REPO}"
            }
        }

        /* ===================== STASH ===================== */
        stage('Stash Source') {
            
            steps {
                stash includes: '**/*', name: 'source-code'
            }
        }

        /* ===================== BUILD ===================== */
        stage('Build') {
            steps {
                echo "No build needed for MERN project - required package.json file"
            }
        }

        

        

        /* ===================== DOCKER BUILD ===================== */
        stage('Docker Build') {
            

            steps {
                unstash 'source-code'

                sh """
                docker build -t ${DOCKERHUB_USER}/${frontend_image}:${BUILD_NUMBER} / client .
                docker tag ${DOCKERHUB_USER}/${frontend_image}:${BUILD_NUMBER} ${DOCKERHUB_USER}/${frontend_image}:latest

                docker build -t ${DOCKERHUB_USER}/${backend_image}:${BUILD_NUMBER} / server .
                docker tag ${DOCKERHUB_USER}/${backend_image}:${BUILD_NUMBER} ${DOCKERHUB_USER}/${backend_image}:latest
                """
            }
        }

        

        /* ===================== PUSH ===================== */
        stage('Push Image') {
            

            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {

                    sh """
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    docker push ${DOCKERHUB_USER}/${frontend_image}:${BUILD_NUMBER}
                    docker push ${DOCKERHUB_USER}/${frontend_image}:latest

                    docker push ${DOCKERHUB_USER}/${backend_image}:${BUILD_NUMBER}
                    docker push ${DOCKERHUB_USER}/${backend_image}:latest
                    """
                }
            }
        }
    }

    post {
        success {
            echo "✅ CI/CD Pipeline SUCCESS"
        }
        failure {
            echo "❌ CI/CD Pipeline FAILED"
        }
    }
}
