pipeline {
    agent any
    environment {
        MYSQL_ROOT_PASSWORD = credentials("MYSQL_ROOT_PASSWORD")
        YOUR_NAME = credentials("YOUR_NAME")
    }
    stages {
        stage('Build') {
            steps {
                sh '''
                docker build -t 52pbailey/task2-flask-app:v${BUILD_NUMBER} flask-app
                docker build -t 52pbailey/task2-db:v${BUILD_NUMBER} db
                '''
            }

        }
        stage('Push') {
            steps {
                sh '''
                docker push 52pbailey/task2-flask-app:v${BUILD_NUMBER}
                docker push 52pbailey/task2-db:v${BUILD_NUMBER}
                '''
            }

        }
        stage('Stage Deploy') {
            steps {
                sh '''
                sed -e 's,{{MYSQL_ROOT_PASSWORD}},'${MYSQL_ROOT_PASSWORD}',g;' sql-password.yaml | kubectl apply -f - --namespace stage
                kubectl apply -f config-map-manifest.yaml --namespace stage
                sed -e 's,{{version}},'${BUILD_NUMBER}',g;' my-sql-manifest.yaml | kubectl apply -f - --namespace stage
                sed -e 's,{{version}},'${BUILD_NUMBER}',g;' -e 's,{{YOUR_NAME}},'${YOUR_NAME}',g;' flask-app-manifest.yaml | kubectl apply -f - --namespace stage
                kubectl apply -f nginx-manifest.yaml --namespace stage
                sleep 60
                kubectl get services
                '''
            }

        }
        stage('Quality Check') {
            steps {
                sh '''
                sleep 50
                export STAGING_IP=\$(kubectl get svc -o json --namespace stage | jq '.items[] | select(.metadata.name == "nginx") | .status.loadBalancer.ingress[0].ip' | tr -d '"')
                echo STAGING_IP
                pip3 install requests
                python3 flask-app/test-app.py
                '''
            }

        }
        stage('Prod Deploy') {
            steps {
                sh '''
                sed -e 's,{{MYSQL_ROOT_PASSWORD}},'${MYSQL_ROOT_PASSWORD}',g;' sql-password.yaml | kubectl apply -f - --namespace prod
                kubectl apply -f config-map-manifest.yaml --namespace prod
                sed -e 's,{{version}},'${BUILD_NUMBER}',g;' my-sql-manifest.yaml | kubectl apply -f - --namespace prod
                sed -e 's,{{version}},'${BUILD_NUMBER}',g;' -e 's,{{YOUR_NAME}},'${YOUR_NAME}',g;' flask-app-manifest.yaml | kubectl apply -f - --namespace prod
                kubectl apply -f nginx-manifest.yaml --namespace prod
                sleep 60
                kubectl get services
                '''
            }

        }

    }

}