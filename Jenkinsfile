pipeline {
    agent any
[2;2R[3;1R[>77;30708;0c]10;rgb:bfbf/bfbf/bfbf]11;rgb:0000/0000/0000[>4;2m
    stages {

        stage('Pull Source') {
            steps {
                checkout scm
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
                    sh 'docker run -d -p 8080:8080 --name infra-demo infra-demo'
                }
            }
        }
    }
}

