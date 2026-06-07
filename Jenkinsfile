pipeline {
    agent any

    tools {
        maven 'Maven'
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

        stage('4 - Package') {
            steps {
                sh 'mvn package -DskipTests'
            }
        }

        stage('5 - Docker Build') {
            steps {
                sh 'docker build -t demo-app:v1 .'
            }
        }

        stage('6 - Verify Docker Image') {
            steps {
                sh 'docker images'
            }
        }
    }

    post {
        success {
            echo 'Pipeline SUCCESS'
        }
        failure {
            echo 'Pipeline FAILED'
        }
    }
}
