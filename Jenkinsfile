pipeline {
    agent any

    environment {
        SONARQUBE_SERVER = 'SonarQubeServer'  // The name of the SonarQube server configured in Jenkins
        SONAR_TOKEN = 'sqa_cb8ae931c499e78ef88de3cdba46c08680a7bb97' // Store the token securely
        DOCKERHUB_CREDENTIALS_ID = 'Docker_Hub_ID_jenkins'
        DOCKERHUB_REPO = 'juhosource/inclass174'
        DOCKER_IMAGE_TAG = 'latest_v1'

    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Juho-source/sep2_week5_inclass_s2.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQubeServer') {
                    sh """
                    export PATH=$PATH:/usr/local/sonar-scanner/bin
                    sonar-scanner \
                    -Dsonar.projectKey=devops-demo \
                    -Dsonar.sources=src \
                    -Dsonar.projectName=DevOps-Demo \
                    -Dsonar.host.url=http://localhost:9000 \
                    -Dsonar.login=${env.SONAR_TOKEN} \
                    -Dsonar.java.binaries=target/classes
                    """
                }
            }
        }
        stage('Docker Build') {
            steps {
                script {
                    def dockerImage = docker.build("myapp:${env.BUILD_ID}")
                    dockerImage.push()
                }
            }
        }
        stage('Push Docker Image to Docker Hub') {
            steps {
                   // Push Docker image to Docker Hub
                   script {
                        docker.withRegistry('https://index.docker.io/v1/', DOCKERHUB_CREDENTIALS_ID) {
                        docker.image("${DOCKERHUB_REPO}:${DOCKER_IMAGE_TAG}").push()
                        }
                      }
                    }
                }

    }
}
