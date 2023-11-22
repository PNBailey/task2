pipeline {
    agent any
    environment {
        YOUR_NAME = credentials("YOUR_NAME")
    }
    stages {
        stage('Build') {
            steps {
                sh '''
                docker build -t 52pbailey/task2-flask-app flask-app
                docker build -t 52pbailey/task2-db db
                '''
            }

        }
        stage('Push') {
            steps {
                sh '''
                docker push 52pbailey/task2-flask-app
                docker push 52pbailey/task2-db
                '''
            }

        }
        stage('Deploy') {
            steps {
                sh '''
                kubectl apply -f .
                kubectl get services
                '''
            }

        }

    }

}