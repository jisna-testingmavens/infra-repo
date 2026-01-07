pipeline {
  agent any

  parameters {
    string(name: 'COMMIT_ID', description: 'Image tag (git commit id)')
  }

  stages {
    stage("Deploy to DigitalOcean Kubernetes") {
      steps {
        sh '''
        sed -i "s/REPLACE_ME/$COMMIT_ID/g" microservice-1-deployment.yaml
        sed -i "s/REPLACE_ME/$COMMIT_ID/g" microservice-2-deployment.yaml
        sed -i "s/REPLACE_ME/$COMMIT_ID/g" microservice-3-deployment.yaml

        kubectl apply -f microservice-1-deployment.yaml
        kubectl apply -f microservice-2-deployment.yaml
        kubectl apply -f microservice-3-deployment.yaml
        '''
      }
    }
  }
}
