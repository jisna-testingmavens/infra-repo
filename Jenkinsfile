pipeline {
  agent any

  parameters {
    string(
      name: 'COMMIT_ID',
      description: 'Git commit ID used as Docker image tag'
    )
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

    stage("Deploy microservice-1") {
      steps {
        sh '''
        helm upgrade --install microservice-1 helm/microservice \
          --set image.tag=${COMMIT_ID}
        '''
      }
    }

    stage("Deploy microservice-2") {
      steps {
        sh '''
        helm upgrade --install microservice-2 helm/microservice \
          --set image.tag=${COMMIT_ID}
        '''
      }
    }

    stage("Deploy microservice-3") {
      steps {
        sh '''
        helm upgrade --install microservice-3 helm/microservice \
          --set image.tag=${COMMIT_ID}
        '''
      }
    }
  }
}
