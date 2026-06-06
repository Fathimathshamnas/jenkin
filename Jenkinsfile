pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "shamnasp/my-devops-app"
        DOCKER_TAG = "${BUILD_NUMBER}"
        SONAR_TOKEN = credentials('sonar-token')
    }

    tools {
        maven 'Maven3'
    }

    stages {

        stage('1 - Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Fathimathshamnas/jenkin.git'
            }
        }

        stage('2 - Maven Build') {
            steps {
                sh 'mvn clean compile'
            }
        }

        stage('3 - Unit Tests') {
            steps {
                sh 'mvn test'
            }
        }

        stage('4 - SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh '''mvn sonar:sonar \
                        -Dsonar.projectKey=my-devops-app \
                        -Dsonar.host.url=http://localhost:9000 \
                        -Dsonar.login=${SONAR_TOKEN}'''
                }
            }
        }

        stage('5 - Quality Gate') {
            steps {
                timeout(time: 2, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('6 - Docker Build') {
            steps {
                sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
                echo "Image built: ${DOCKER_IMAGE}:${DOCKER_TAG}"
            }
        }

        stage('7 - Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS')]) {
                    sh "docker login -u ${DOCKER_USER} -p ${DOCKER_PASS}"
                    sh "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}"
                }
            }
        }
    }

    post {
        success { echo 'Pipeline SUCCESS! Image pushed to Docker Hub.' }
        failure  { echo 'Pipeline FAILED — check logs.' }
    }
}
