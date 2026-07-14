pipeline {
    agent any

    tools {
        maven 'Maven3'
        jdk 'JDK21'
    }

    environment {
        IMAGE_NAME = "employee-app"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Compile') {
            steps {
                sh 'mvn clean compile'
            }
        }

        stage('Unit Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Package') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t employee-app .'
            }
        }

        stage('Remove Old Container') {
    steps {
        sh '''
        docker stop employee-app || true
        docker rm employee-app || true
        docker stop employee-container || true
        docker rm employee-container || true
        '''
    }
}

        stage('Run New Container') {
            steps {
                sh '''
                docker run -d \
                --name employee-app \
                -p 8087:8087 \
                employee-app
                '''
            }
        }

        stage('Health Check') {
            steps {
                sh 'sleep 20'
                sh 'curl http://localhost:8087/employees'
            }
        }
    }
}
