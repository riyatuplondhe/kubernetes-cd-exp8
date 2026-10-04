pipeline {
    agent any

    environment {
        REG = 'riyatuplondhe'
        IMAGE = 'riyatuplondhe/flask-demo'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build & Push Image') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-creds',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {
                    sh 'echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin'
                    sh 'docker build -t $IMAGE:$BUILD_NUMBER .'
                    sh 'docker push $IMAGE:$BUILD_NUMBER'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                    kubectl set image deployment/web-deploy \
                    web=$IMAGE:$BUILD_NUMBER

                    kubectl rollout status deployment/web-deploy
                '''
            }
        }
    }
}
