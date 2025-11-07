pipeline {
    agent any

    environment {
        DB_URL = credentials('mysql-url')
        DB_USER = credentials('mysql-username')
        DB_PASS = credentials('mysql-password')
        JWT_SECRET = credentials('jwt-secret')
    }

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
                sh "docker build -t infra-demo ."
            }
        }

        stage('Stop Old Container') {
            steps {
                sh """
                    docker stop infra-demo || true
                    docker rm infra-demo || true
                """
            }
        }

        stage('Run New Container') {
            steps {
                sh """
                    docker run -d \
                      --name infra-demo \
                      --network infra-net \
                      -p 8080:8080 \
                      -e SPRING_PROFILES_ACTIVE=dev \
                      -e SPRING_DATASOURCE_URL=$DB_URL \
                      -e SPRING_DATASOURCE_USERNAME=$DB_USER \
                      -e SPRING_DATASOURCE_PASSWORD=$DB_PASS \
                      -e SPRING_JWT_SECRET=$JWT_SECRET \
                      infra-demo
                """
            }
        }
    }
}

