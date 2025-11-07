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
                sh """
                    docker build -t infra-demo .
                """
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
                      -e SPRING_JWT_SECRET="1nGmLQMnCQri6kT7jCy0rSzzcAJNR8BoZzDYr/tcVTnwZ/173LuUX3gAwZlI6NRExH0CffzkizyE75VX1Mqw7w==" \
                      infra-demo
                """
            }
        }
    }
}

