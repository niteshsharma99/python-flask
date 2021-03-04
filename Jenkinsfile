pipeline {
  environment {
  IBM_CLOUD_REGION = 'us-south'
  IKS_CLUSTER = 'c0uvrhfd0v1b3bd432lg'
  registry = "nitesh99sharma/test"
  registryCredential = 'dockerhub_id'
  dockerImage = ''
  }
  agent any
  stages {
    stage('Install IBM Cloud CLI') {
      steps {
        sh '''
            curl -fsSL https://clis.cloud.ibm.com/install/linux | sh
            ibmcloud --version
            ibmcloud config --check-version=false
            ibmcloud plugin install -f kubernetes-service
            ibmcloud plugin install -f container-registry
            '''
      }
    }
    stage('Authenticate with IBM Cloud CLI') {
      steps {
        sh '''
            ibmcloud login --apikey ${IBM_API_KEY} -r "${IBM_CLOUD_REGION}" -g Default
            ibmcloud ks cluster config --cluster ${IKS_CLUSTER}
            '''
      }
    }
    stage('Build with Docker') {
      steps {
        script {
        dockerImage = docker.build registry
        }
      }
    }
    stage('Push the image to ICR') {
      steps {
        script {
          docker.withRegistry( '', registryCredential ) {
          dockerImage.push()
          }
        }
      }
    }
    stage('Deploy to IKS') {
      steps {
        sh '''
            ibmcloud ks cluster config --cluster ${IKS_CLUSTER}
            kubectl config current-context
            kubectl apply -f deploy.yaml
            kubectl apply -f deployment.yml
            kubectl apply -f service.yml
            kubectl apply -f ingress.yml
            '''
       }
     }
   }
 }