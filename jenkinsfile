pipeline {
    agent none

    options {
        timestamps()
        disableConcurrentBuilds()
    }

    environment {
        // DockerHub Configuration
        DOCKERHUB_USERNAME = 'vismayavinodkk'
        DOCKERHUB_REPOSITORY = 'company-website'
        DOCKERHUB_CREDENTIAL_ID = 'dockerhub-credentials'

        // Deployment Server
        DEPLOYMENT_SERVER_IP = '13.207.203.210'

        // GitHub Configuration
        GITHUB_REPOSITORY_URL = 'https://github.com/vismayavinodkk01-creator/company-website.git'

        // Application Configuration
        APP_NAME = 'company-website'
        IMAGE_NAME = "${DOCKERHUB_USERNAME}/${DOCKERHUB_REPOSITORY}"
    }

    stages {

        // STAGE 1: CHECKOUT ON BUILD SERVER
        stage('Checkout') {
            agent {
                label 'build-server'
            }

            steps {
                echo 'Checking out source code from GitHub...'

                git branch: 'main',
                    url: "${GITHUB_REPOSITORY_URL}"
            }
        }

        // STAGE 2: BUILD AND PUSH ON BUILD SERVER
        stage('Build and Push Docker Image') {
            agent {
                label 'build-server'
            }

            steps {
                script {
                    env.DEPLOY_IMAGE =
                        "${env.IMAGE_NAME}:${env.BUILD_NUMBER}"

                    echo "Building Docker Image: ${env.DEPLOY_IMAGE}"

                    sh '''
                        set -eu

                        docker build --pull \
                          -t "$DEPLOY_IMAGE" .

                        docker tag \
                          "$DEPLOY_IMAGE" \
                          "$IMAGE_NAME:latest"
                    '''

                    withCredentials([
                        usernamePassword(
                            credentialsId: "${DOCKERHUB_CREDENTIAL_ID}",
                            usernameVariable: 'DOCKER_USER',
                            passwordVariable: 'DOCKER_PASSWORD'
                        )
                    ]) {

                        sh '''
                            set +x

                            printf '%s' "$DOCKER_PASSWORD" |
                              docker login \
                                --username "$DOCKER_USER" \
                                --password-stdin

                            docker push "$DEPLOY_IMAGE"

                            docker push "$IMAGE_NAME:latest"

                            docker logout
                        '''
                    }

                    echo 'Docker image pushed successfully!'
                }
            }
        }

        // STAGE 3: DEPLOY ON DEPLOY SERVER
        stage('Deploy to Deployment Server') {
            agent {
                label 'deploy-server'
            }

            steps {
                echo 'Deploying application on deployment server...'

                sh '''
                    set -eu

                    echo "Pulling Docker image..."

                    docker pull "$DEPLOY_IMAGE"

                    echo "Removing old container..."

                    docker rm -f "$APP_NAME" 2>/dev/null || true

                    echo "Starting new container..."

                    docker run -d \
                      --name "$APP_NAME" \
                      --restart unless-stopped \
                      -p 9090:80 \
                      "$DEPLOY_IMAGE"

                    echo "Verifying deployment..."

                    for attempt in $(seq 1 15); do

                        if curl -fsS \
                          http://localhost:9090/ \
                          >/dev/null; then

                            echo "Deployment verification successful!"
                            exit 0
                        fi

                        sleep 2
                    done

                    echo "Deployment verification failed!" >&2

                    docker logs "$APP_NAME" || true

                    exit 1
                '''
            }
        }
    }

    post {
        success {
            echo '''
                ========================================
                SUCCESS: COMPANY WEBSITE DEPLOYED!
                ========================================
                DockerHub: vismayavinodkk/company-website
                Deployment Port: 9090
                ========================================
            '''
        }

        failure {
            echo '''
                ========================================
                PIPELINE FAILED!
                Check Jenkins console output.
                ========================================
            '''
        }

        always {
            echo 'Jenkins pipeline execution completed.'
        }
    }
}
      

                

                   
