pipeline{
    tools{
        maven 'Maven3'
    }
    agent any
    environment{
         registry = "654698928147.dkr.ecr.ap-south-1.amazonaws.com/my-docker-repo"
    }
    stages{
        stage('Cloning Git'){
            steps {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[https://github.com/ns-mastan/deployment.git]])
            }
        }
        stage('Build Maven'){
            steps{
                sh 'mvn clean install' 
            }
            
        }
        stage('Build Image'){
            steps{
                script{
                    dockerImage = docker.build registry 
                }
            }
        }
         stage('Pushing to ECR') {
             steps{  
                 script {
                     sh 'aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 654698928147.dkr.ecr.ap-south-1.amazonaws.com'
                     sh 'docker push 654698928147.dkr.ecr.ap-south-1.amazonaws.com/my-docker-repo'
                }
             }
        }
        stage ('K8S Deploy') {
            steps {
                script {
                    kubernetesDeploy(configs: "Deployment.yaml", "Service.yaml")
                }
            }   
        }
    }
}
