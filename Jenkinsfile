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
       withDockerRegistry(credentialsId: 'ecr:us-east-1:ecr-credentials', url: 'http://224605180493.dkr.ecr.us-east-1.amazonaws.com/address-service')
        {
          sh 'docker tag address-service:latest 224605180493.dkr.ecr.us-east-1.amazonaws.com/address-service:v1'
          sh 'docker push 224605180493.dkr.ecr.us-east-1.amazonaws.com/address-service:v1'
        } 
      }
    }
  /** stage('deploy to Eks') {
      steps {
          kubernetesDeploy(configs: '.kube/config', enableConfigSubstitution: false , kubeConfig: [path: '.aws/config'], kubeconfigId: 'kubeconfig',ssh: [sshCredentialsId: '*', sshServer: ''], textCredentials: [certificateAuthorityData: '', clientCertificateData: '', clientKeyData: '', serverUrl: 'https://4f4d2b665408ce1b3d8d9e80c165c7dd.gr7.us-east-1.eks.amazonaws.com'])
        {
         sh 'kubectl apply -f deployment.yaml --namespace=2048-game' 
         sh 'kubectl apply -f service.yaml--namespace=2048-game'
        }
      }
    } **/
    stage('Deploy In AWS EKS-Fargate) { 
          steps {
          kubernetesDeploy(
	             configs: 'deployment.yaml'
			         kubeconfigId: 'KUBERNETES_CLUSTER_CONFIG'
			         enableConfigSubstitution: true
		      )	
          }
     }
  }
}
