pipeline {
  agent any

  stages {
    stage("Deploy to DigitalOcean Kubernetes") {
      steps {
        sh '''
        kubectl apply -f microservice-1-deployment.yaml
        kubectl apply -f microservice-2-deployment.yaml
        kubectl apply -f microservice-3-deployment.yaml
        '''
      }
    }
  }
}
