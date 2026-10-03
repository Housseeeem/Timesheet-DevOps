pipeline {
    agent any

    environment {
        REGISTRY = 'localhost:5000'
        IMAGE = 'backend-app'
        NETWORK = 'timesheet-net'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build & Test Maven') {
            steps {
                sh 'mvn clean verify'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t ${IMAGE}:latest .'
                sh 'docker tag ${IMAGE}:latest ${REGISTRY}/${IMAGE}:latest'
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'registry-creds', usernameVariable: 'REG_USER', passwordVariable: 'REG_PASS')]) {
                    sh 'echo "$REG_PASS" | docker login ${REGISTRY} -u "$REG_USER" --password-stdin'
                    sh 'docker push ${REGISTRY}/${IMAGE}:latest'
                    sh 'docker logout ${REGISTRY}'
                }
            }
        }

        stage('Deploy MySQL') {
            steps {
                sh 'docker network create ${NETWORK} || true'
                sh 'docker rm -f mysql || true'
                sh '''
                    docker run -d --name mysql --network ${NETWORK} \
                      -p 3306:3306 \
                      -e MYSQL_ALLOW_EMPTY_PASSWORD=yes \
                      -e MYSQL_DATABASE=timesheet-devops-db \
                      mysql:8.0
                '''
                sh '''
                    for i in $(seq 1 30); do
                      docker exec mysql mysqladmin ping --silent && break
                      sleep 3
                    done
                '''
            }
        }

        stage('Deploy backend-app') {
            steps {
                sh 'docker rm -f backend-app || true'
                withCredentials([usernamePassword(credentialsId: 'registry-creds', usernameVariable: 'REG_USER', passwordVariable: 'REG_PASS')]) {
                    sh 'echo "$REG_PASS" | docker login ${REGISTRY} -u "$REG_USER" --password-stdin'
                    sh 'docker pull ${REGISTRY}/${IMAGE}:latest'
                    sh 'docker logout ${REGISTRY}'
                }
                sh '''
                    docker run -d --name backend-app --network ${NETWORK} \
                      -p 8082:8082 \
                      -e "SPRING_DATASOURCE_URL=jdbc:mysql://mysql:3306/timesheet-devops-db?useUnicode=true&serverTimezone=UTC" \
                      ${REGISTRY}/${IMAGE}:latest
                '''
            }
        }

        stage('Vérification') {
            steps {
                sh 'sleep 20'
                sh 'docker ps'
                sh 'docker logs backend-app'
            }
        }
    }
}
