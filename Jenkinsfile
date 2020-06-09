pipeline {
  environment {
    registry = "ankababu/k8s-java"
    registryCredential = 'docker-hub'
    dockerImage = ''
  }
  agent any
  stages {
    stage('Cloning Git') {
      steps {
        git 'https://github.com/ankababug/k8s.git'
      }
    }
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build registry + ":$BUILD_NUMBER"
        }
      }
    }
    stage('Deploy Image') {
      steps{
        script {
          docker.withRegistry( '', registryCredential ) {
            dockerImage.push()
          }
        }
      }
    }
    stage('deploy to k8s') {
      steps{
        sshagent(['kops-machine']){
          sh "scp -o StrictHostKeyChecking=no pods.yml centos@172.31.0.218:/home/centos/"
          script{
              try{
                 sh "ssh centos@172.31.0.218 kubectl apply -f ."
              }catch(error){
                 sh "ssh centos@172.31.0.218 kubectl create -f ."
              }
           }
        }
      }
    }
  }
}
