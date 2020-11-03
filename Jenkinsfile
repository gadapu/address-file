pipeline {
  agent any
  tools { 
        maven 'Maven'
        jdk 'Java'
  }
  stages {
    stage('Clone repository') {
        /* Let's make sure we have the repository cloned to our workspace... */
      steps {
        checkout scm
      }
    }
    stage('Build') {
      steps {
        sh 'mvn -B -DskipTests clean package'
        sh 'echo $USER'
        sh 'echo whoami'
      }
    }
    stage('Docker Build') {
      steps {
        sh 'docker build -t address-service .'
      }
    }
   
    stage('push image to ECR'){
      steps {
       withDockerRegistry(credentialsId: 'ecr:us-east-1:5e8f8e1c-63d5-4626-a902-5b34484d8c93', url: 'http://224605180493.dkr.ecr.us-east-1.amazonaws.com/address-service')
        {
          sh 'docker tag address-service:latest 224605180493.dkr.ecr.us-east-1.amazonaws.com/address-service:v1'
          sh 'docker push 224605180493.dkr.ecr.us-east-1.amazonaws.com/address-service:v1'
        } 
      }
    }
  stage('deploy to Eks') {
      steps {
         sh 'kubectl apply -f deployment.yaml --namespace=2048-game' 
         sh 'kubectl apply -f service.yaml--namespace=2048-game'
        }
      }
    } 
  }
