pipeline {
    agent {
        docker {
            image 'node:latest' // Example Docker image with Node.js
            args '-v /var/run/docker.sock:/var/run/docker.sock' // Mount Docker socket
        }
    }
    environment {
        CI = 'true'
        registry = "kaursimar542/docker"
        registryCredential = 'dockerhub'
    }

    stages {
         stage('Initialize') {
            steps {
                script {
                    def dockerHome = tool 'myDocker'
                    env.PATH = "${dockerHome}/bin:${env.PATH}"
                }
            }
        }
        stage('Build') {
            steps {
                sh 'npm install'
            }
        }
        stage('Test') {
            steps {
                sh './jenkins/scripts/test.sh'
            }
        }
        stage('make image') {
            steps {
                script {
                    dockerImage = docker.build registry + ":latest"
                }
                // sh 'docker build -t simple-node-js-react:latest .'
            }
        }
        stage('push image') {
            steps {
                script {
                    docker.withRegistry( '', registryCredential )
                    dockerImage.push()
                // sh 'docker push simple-node-js-react-npm'
                }
            }
        }
        stage('Deliver') {
            steps {
                sh './jenkins/scripts/deliver.sh'
                input message: 'Finished using the web site? (Click "Proceed" to continue)'
                sh './jenkins/scripts/kill.sh'
            }
        }
    }
}
