pipeline {
    agent any 
    environment {
        registryCredential = 'dockerhub'
        imageName = 'nareshg4/external:v1.3'
        dockerImage = ''
        }
    stages {
        stage('Run the tests') {
             agent {
                docker { 
                    image 'node:14-alpine'
                    args '-e HOME=/tmp -e NPM_CONFIG_PREFIX=/tmp/.npm'
                    reuseNode true
                }
            }
            steps {
                echo 'Retrieve source from github. run npm install and npm test'
                git branch: 'master',
                    url: 'https://github.com/nareshg4/NodeJS_Demo_External.git'
                echo 'showing files from repo?' 
                sh 'ls -a'
                echo 'install dependencies' 
                sh 'npm install'
                echo 'Run tests'
                sh 'npm test'
                echo 'Testing completed' 
            }
        }
        stage('Building image') {
            steps{
                script {
                    echo 'build the image' 
                }
            }
            }
        stage('Push Image') {
            steps{
                script {
                    echo 'push the image to docker hub' 
                }
            }
        }     
         stage('deploy to k8s') {
             agent {
                docker { 
                    image 'google/cloud-sdk:latest'
                    args '-e HOME=/tmp'
                    reuseNode true
                        }
                    }
            // steps {
            //  }
        }     
        // stage('Remove local docker image') {
        //     steps{
        //         sh "docker rmi $imageName:latest"
        //         sh "docker rmi $imageName:$BUILD_NUMBER"
        //     }
        // }
    }
}