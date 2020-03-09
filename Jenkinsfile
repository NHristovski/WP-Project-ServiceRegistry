pipeline {

    environment {
        registry = "nikolancaid/service-registry"
        registryCredential = 'dockerhub'
        dockerImage = ''

    }
    agent any

    stages {
        stage('Git clone') {
            steps {
                git 'https://github.com/NHristovski/WP-Project-ServiceRegistry.git'
            }
        }

        stage('Building docker image') {
            steps {
                script {
                    dockerImage = docker.build registry + ":latest"
                }
            }
        }

        stage('Deploy Image') {
            steps {
                script {
                    docker.withRegistry('', registryCredential) {
                        dockerImage.push()
                    }
                }
            }
        }

        stage('Remove Unused docker image') {
            steps {
                sh "docker rmi $registry:latest"
            }
        }


        stage('Pull docker image on host') {
            steps {
                sh 'ssh -o StrictHostKeyChecking=no nhristov@10.10.10.57 "cat ~/docker_password.txt | docker login --username nikolancaid --password-stdin"'

                try {
                    sh 'ssh -o StrictHostKeyChecking=no nhristov@10.10.10.57 "docker rm -f service-registry"'
                } catch (Exception e) {
                    sh 'echo ======= Failed to delete container service-registry'
                }

                sh 'ssh -o StrictHostKeyChecking=no nhristov@10.10.10.57 "docker pull nikolancaid/service-registry:latest"'
            }
        }

        stage('Start the application'){
            steps {
                sh 'ssh -o StrictHostKeyChecking=no nhristov@10.10.10.57 "docker run --name service-registry -p 8761:8761 -d nikolancaid/service-registry:latest"'
            }
        }
    }
}
