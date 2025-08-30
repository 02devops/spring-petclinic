pipeline {
    agent any

    environment {
        IMAGE_NAME = 'dharam3030/spring-petclinic'
        CONTAINER_PORT = '8080'
        HOST_PORT = '9090'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/02devops/spring-petclinic.git'
            }
        }

        stage('Build JAR') {
            steps {
                sh 'mvn clean package -DskipTests -Dmaven.compiler.release=17'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds', 
                    usernameVariable: 'DOCKER_USERNAME', 
                    passwordVariable: 'DOCKER_PASSWORD'
                )]) {
                    sh '''
                        echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin
                        docker push $IMAGE_NAME
                    '''
                }
            }
        }

        stage('Clean Running Container') {
            steps {
                sh '''
                    docker stop spring-petclinic || true
                    docker rm spring-petclinic || true
                '''
            }
        }

        stage('Deploy Container') {
            steps {
                sh 'docker run -d --name spring-petclinic -p $HOST_PORT:$CONTAINER_PORT $IMAGE_NAME'
            }
        }
    }

    post {
        failure {
            echo '❌ Build or deployment failed!'
        }
        success {
            echo '✅ Build, push, and deployment successful!'
        }
    }
}
