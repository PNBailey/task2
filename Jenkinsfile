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
                docker build -t 52pbailey/task2-nginx nginx
                docker build -t 52pbailey/task2-db db
                '''
            }

        }
        stage('Push') {
            steps {
                sh '''
                docker push 52pbailey/task2-flask-app
                docker push 52pbailey/task2-nginx
                docker push 52pbailey/task2-db
                '''
            }

        }
        stage('Deploy') {
            steps {
                sh '''
                ssh jenkins@paulb-deploy <<EOF
                export YOUR_NAME=${YOUR_NAME}

                docker pull 52pbailey/task2-flask-app
                docker pull 52pbailey/task2-nginx
                docker pull 52pbailey/task2-db

                docker network rm task2-net && echo "removed network" || echo "network already removed"
                docker network create task2-net

                docker stop flask-app && echo "Stopped flask-app" || echo "flask-app is not running"
                docker rm flask-app && echo "removed flask-app" || echo "flask-app does not exist"

                docker stop nginx && echo "Stopped nginx" || echo "nginx is not running"
                docker rm nginx && echo "removed nginx" || echo "nginx does not exist"

                docker stop mysql && echo "Stopped mysql" || echo "mysql is not running"
                docker rm mysql && echo "removed mysql" || echo "mysql does not exist"

                docker run -d --name mysql --network task2-net -e MYSQL_ROOT_PASSWORD=Password123 52pbailey/task2-db
                docker run -d --name flask-app --network task2-net -e YOUR_NAME=${YOUR_NAME} -e MYSQL_ROOT_PASSWORD=Password123 52pbailey/task2-flask-app
                docker run -d -p 80:80 --name nginx --network task2-net 52pbailey/task2-nginx
                '''
            }

        }

    }

}