pipeline {
    agent any
    
    environment {
        //imagename = "ramisetty32/mywebapp:latest"
        DOCKERHUB_CREDENTIALS=credentials('docker_hub')
        REMOTE_USER='ubuntu'
        REMOTE_SERVER='35.88.136.192'
        
        }
    
    stages {
        stage('checkout'){
            steps {
                echo "its a cloning stage"
                git branch: 'main', url: 'https://github.com/ramisetty1/DevOps-CI-CD-Project.git'
            }
        }
        stage('build') {
            steps {
                echo "build stage"
                sh '/opt/maven/bin/mvn clean package'
            }
            post {
                success {
                  archiveArtifacts artifacts: '**/target/*.jar'
                }
            }
        }
        
        stage('docker_image') {
            steps {
                echo "building docker image "
                sh 'docker build -t mywebapp .'
                sh 'docker tag mywebapp ramisetty32/mywebapp:latest'
            }
        }
        /*stage('Push'){
            steps{
                withCredentials([usernamePassword(credentialsId: 'docker_hub', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
        	     sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}"
                 sh 'docker push mywebapp ramisetty32/mywebapp:latest'
                }
            }
        }*/
        
        stage('Docker Login') {
            steps{
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }
        
        stage('uplaod') {
            steps {
                sh 'docker push ramisetty32/mywebapp:latest'
            }
        }
        
        
        stage('deploy') {
            steps {
                sshagent(['aws_login']) {
                    //sh "ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${REMOTE_SERVER} 'docker stop javaApp || true && docker rm javaApp || true'" 
                    sh "ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${REMOTE_SERVER} 'docker pull ramisetty32/mywebapp:latest'" 
                    sh "ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${REMOTE_SERVER} 'docker run --name mywebapp -d -p 8081:8081 ramisetty32/mywebapp:latest'"
    
            }
            }
        }
        
    }
}
