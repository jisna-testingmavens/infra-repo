pipeline {
  agent any

  parameters {
    string(name: 'COMMIT_ID', description: 'Git commit id for image tag')
  }

  stages {
    stage("Validate Input") {
      steps {
        script {
          if (!params.COMMIT_ID?.trim()) {
            error "COMMIT_ID is empty. Aborting deployment."
          }
        }
      }
    }

    stage("Deploy to DigitalOcean Kubernetes") {
      steps {
        sh '''
        sed -i "s/REPLACE_ME/${COMMIT_ID}/g" microservice-1-deployment.yaml
        sed -i "s/REPLACE_ME/${COMMIT_ID}/g" microservice-2-deployment.yaml
        sed -i "s/REPLACE_ME/${COMMIT_ID}/g" microservice-3-deployment.yaml

        kubectl apply -f microservice-1-deployment.yaml
        kubectl apply -f microservice-2-deployment.yaml
        kubectl apply -f microservice-3-deployment.yaml
        '''
      }
    }
  }
}
