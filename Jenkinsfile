pipeline {
    agent any

    stages {

        stage('Pull Source') {
            steps {
                checkout scm
            }
        }

        stage('Set Gradle Permission') {
            steps {
                sh 'chmod +x ./gradlew'
            }
        }

        stage('Build JAR') {
            steps {
                sh './gradlew clean build -x test'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t infra-demo .'
                }
            }
        }

        stage('Stop & Remove Old Container') {
            steps {
                script {
                    sh 'docker stop infra-demo || true'
                    sh 'docker rm infra-demo || true'
                }
            }
        }

        stage('Run New Container') {
            steps {
                script {
                    sh 'docker run -d -p 8080:8080 --name infra-demo \ -e spring.jwt.secret=qwe123 \ infra-demo'
                }
            }
        }
    }
}

