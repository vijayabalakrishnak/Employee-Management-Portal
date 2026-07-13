pipeline {

    agent any

    tools {
        maven 'Maven'
        jdk 'JDK17'
    }

    environment {
        IMAGE_NAME = "yourdockerhubusername/employee-app"
        CONTAINER = "employee-app"
    }

    stages {

        stage('Checkout') {
            steps {
                git 'https://github.com/yourusername/Employee-Management-Portal.git'
            }
        }

        stage('Compile') {
            steps {
                sh 'mvn compile'
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
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Remove Old Container') {
            steps {
                sh '''
                docker stop employee-app || true
                docker rm employee-app || true
                '''
            }
        }

        stage('Run New Container') {
            steps {
                sh '''
                docker run -d \
                --name employee-app \
                -p 8080:8080 \
                $IMAGE_NAME
                '''
            }
        }

        stage('Health Check') {
            steps {
                sh 'sleep 20'
                sh 'curl http://localhost:8080'
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub',
                usernameVariable: 'USER',
                passwordVariable: 'PASS')]) {

                    sh '''
                    echo $PASS | docker login -u $USER --password-stdin
                    docker push $IMAGE_NAME
                    '''
                }
            }
        }

    }

}
