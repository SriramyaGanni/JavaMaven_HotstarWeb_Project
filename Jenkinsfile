pipeline {
    agent any

    environment {
        IMAGE_NAME = "sriramyaganni/hotstar-webapp"
        CONTAINER_NAME = "hotstar-container"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'master',
                credentialsId: 'Sriramya',
                url: 'https://github.com/SriramyaGanni/JavaMaven_HotstarWeb_Project.git'

            }
        }

        stage('Build Maven Artifact') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:${BUILD_NUMBER} .'
                sh 'docker tag $IMAGE_NAME:${BUILD_NUMBER} $IMAGE_NAME:latest'
            }
        }

        stage('DockerHub Login') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh 'echo $PASS | docker login -u $USER --password-stdin'
                }
            }
        }

        stage('Push Image to DockerHub') {
            steps {
                sh 'docker push $IMAGE_NAME:${BUILD_NUMBER}'
                sh 'docker push $IMAGE_NAME:latest'
            }
        }

        stage('Run Container') {
            steps {
                sh '''
                docker rm -f $CONTAINER_NAME || true
                docker run -d -p 8083:8080 --name $CONTAINER_NAME $IMAGE_NAME:latest
                '''
            }
        }
    }
}


