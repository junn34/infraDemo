pipeline {
    agent any

    environment {
        APP_NAME = "infra-demo"
        PORT = "8080"
        JWT_SECRET = "1nGmLQMnCQri6kT7jCy0rSzzcAJNR8BoZzDYr/tcVTnwZ/173LuUX3gAwZlI6NRExH0CffzkizyE75VX1Mqw7w=="
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
                    -e SPRING_JWT_SECRET="$JWT_SECRET" \
                    $APP_NAME
                """
            }
        }
    }

    post {
        success {
            echo "Deployment Successful! App running on http://localhost:$PORT"
        }
        failure {
            echo "Deployment Failed. Check docker logs."
        }
    }

