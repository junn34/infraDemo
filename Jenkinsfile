pipeline {
    agent any

    environment {
        APP_NAME = "infra-demo"
        PORT = "8080"
        JWT_SECRET = "abcd1234"   // 실 서비스에서는 Jenkins Credentials 사용해야 함!
    }

    stages {

        stage('Checkout Source') {
            steps {
                checkout scm
            }
        }

        stage('Build JAR') {
            steps {
                sh 'chmod +x gradlew'
                sh './gradlew clean build -x test'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $APP_NAME .'
            }
        }

        stage('Stop & Remove Old Container') {
            steps {
                sh 'docker stop $APP_NAME || true'
                sh 'docker rm $APP_NAME || true'
            }
        }

        stage('Run New Container') {
            steps {
                sh """
                docker run -d --name $APP_NAME \
                    -p $PORT:$PORT \
                    -e spring.jwt.secret=$JWT_SECRET \
                    $APP_NAME
                """
            }
        }
    }

    post {
        success {
            echo "Deployment Successful! Running on port $PORT"
        }
        failure {
            echo "Deployment Failed. Check logs."
        }
    }
}

