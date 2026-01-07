pipeline {
    agent any

    parameters {
        string(
            name: 'COMMIT_ID',
            description: 'Docker image tag (git commit ID)'
        )
    }

    stages {

        stage("Checkout") {
            steps {
                checkout scm
            }
        }

        stage("Validate Input") {
            steps {
                script {
                    if (!params.COMMIT_ID?.trim()) {
                        error "COMMIT_ID must be provided"
                    }
                    echo "Deploying image tag: ${params.COMMIT_ID}"
                }
            }
        }

        stage("Validate Helm Chart") {
            steps {
                script {
                    if (!fileExists('helm/microservice/Chart.yaml')) {
                        error """
Helm chart not found!

Expected:
  helm/microservice/Chart.yaml

Workspace contents:
${sh(script: 'ls -l', returnStdout: true)}
"""
                    }
                }
            }
        }

        stage("Deploy Microservices") {
            steps {
                dir('helm/microservice') {
                    sh '''
                    set -e

                    for svc in microservice-1 microservice-2 microservice-3; do
                        echo "Deploying $svc with image tag ${COMMIT_ID}"
                        helm upgrade --install $svc . \
                          --set image.tag=${COMMIT_ID}
                    done
                    '''
                }
            }
        }
    }

    post {
        success {
            echo " All microservices deployed successfully"
        }
        failure {
            echo "Infra deployment failed"
        }
    }
}
