pipeline {
    agent any

    parameters {
        string(
            name: 'COMMIT_ID',
            description: 'Docker image tag (git commit ID)'
        )
    }

    stages {

        stage("Validate Input") {
            steps {
                script {
                    if (!params.COMMIT_ID?.trim()) {
                        error "COMMIT_ID must be provided by microservice pipeline"
                    }
                    echo "Deploying image tag: ${params.COMMIT_ID}"
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
