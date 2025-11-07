pipeline {
    agent any

    stages {

        stage('Pull Source') {
            steps {
                checkout scm
            }
        }

        stage('Build JAR') {
            steps {
                sh 'chmod +x ./gradlew'
                sh './gradlew clean build -x test'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh """
                        docker build -t infra-demo .
                    """
                }
            }
        }

        stage('Stop Old Container') {
            steps {
                script {
                    sh """
                        docker stop infra-demo || true
                        docker rm infra-demo || true
                    """
                }
            }
        }

        stage('Run New Container') {
            steps {
                script {
                    sh """
                        docker run -d \
                          --name infra-demo \
                          --network infra-net \
                          -p 8080:8080 \
                          infra-demo
                    """
                }
            }
        }
    }
}

