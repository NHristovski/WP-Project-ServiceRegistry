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
                    dockerImage = docker.build registry + "latest"
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
            steps{
                sh "docker rmi $registry:latest"
            }
        }


        stage('Pull and start docker image on host'){

            def remote = [:]
            remote.name = 'test'
            remote.host = '10.10.10.57'
            remote.user = 'root'
            remote.password = '#Jassum1234'
            remote.allowAnyHosts = true
            
            steps{
                sshCommand remote: remote, command: "docker login -u nikolancaid -p jassum123"
                sshCommand remote: remote, command: "docker pull $registry:latest"
                sshCommand remote: remote, command: "docker start -p 8761:8761 $registry:latest"
            }
        }
    }
}
