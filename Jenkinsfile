pipeline {
    agent any

    parameters {
        string(
            name: 'COMMIT_ID',
            description: 'Docker image tag (git commit ID) to deploy'
        )
    }

    environment {
        HELM_CHART_PATH = "helm/microservice"
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

        stage("Validate Helm Chart") {
            steps {
                sh '''
                echo "Current workspace: $PWD"
                if [ ! -d ${HELM_CHART_PATH} ]; then
                    echo "Error: Helm chart folder ${HELM_CHART_PATH} not found!"
                    exit 1
                fi

                if [ ! -f ${HELM_CHART_PATH}/Chart.yaml ]; then
                    echo "Error: Chart.yaml missing in ${HELM_CHART_PATH}!"
                    exit 1
                fi

                echo "Helm chart found at ${HELM_CHART_PATH}:"
                ls -l ${HELM_CHART_PATH}
                '''
            }
        }

        stage("Deploy Microservices") {
            steps {
                sh '''
                for svc in microservice-1 microservice-2 microservice-3; do
                    echo "Deploying $svc with image tag ${COMMIT_ID}..."
                    helm upgrade --install $svc ${HELM_CHART_PATH} --set image.tag=${COMMIT_ID}
                done
                '''
            }
        }
    }

    post {
        success {
            echo " All microservices deployed successfully with image tag ${params.COMMIT_ID}."
        }
        failure {
            echo " Deployment failed. Check logs above for details."
        }
    }
}
