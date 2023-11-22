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
                sed -e 's, {{MYSQL_ROOT_PASSWORD}}, '${MYSQL_ROOT_PASSWORD}' ,g;' sql-password.yaml | kubectl apply -f -
                kubectl apply -f *-manifest.yaml
                kubectl get services
                '''
            }

        }

    }

}