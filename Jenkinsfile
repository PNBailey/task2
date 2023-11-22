pipeline {
    agent any
    environment {
        MYSQL_ROOT_PASSWORD = credentials("MYSQL_ROOT_PASSWORD")
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
                kubectl create secret generic mysql-root-password --from-literal=mysql-root-password=$MYSQL_ROOT_PASSWORD
                kubectl apply -f .
                kubectl get services
                '''
            }

        }

    }

}