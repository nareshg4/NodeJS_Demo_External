pipeline {
    agent any 
    environment {
        registryCredential = 'dockerhub'
        imageName = 'nareshg4/external:v1.4'
        dockerImage = ''
        }
    stages {
        stage('Run the tests') {
             agent {
                docker { 
                    image 'node:18-alpine'
                    args '-e HOME=/tmp -e NPM_CONFIG_PREFIX=/tmp/.npm'
                    reuseNode true
                }
            }
            steps {
                echo 'Retrieve source from github' 
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
                    echo 'building image' 
                    dockerImage = docker.build("${env.imageName}:${env.BUILD_ID}")
                    echo 'image built'
                }
            }
            }
        stage('Push Image') {
            steps{
                script {
                    echo 'pushing the image to docker hub' 
                    docker.withRegistry('',registryCredential){
                        dockerImage.push("${env.BUILD_ID}")
                    }
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
            steps {
                echo 'Get cluster credentials'
                sh 'gcloud container clusters get-credentials events-feed-cluster --zone us-central1-c --project roidtc-june22-u106'
                sh "kubectl set image nareshg4/external events-external=${env.imageName}:${env.BUILD_ID} --namespace=events"

             }
        }     
        stage('Remove local docker image') {
            steps{
                echo "pending"

                sh "docker rmi ${env.imageName}:${env.BUILD_ID}"
            }
        }
    }
}
