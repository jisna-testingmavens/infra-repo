pipeline {
    agent any

    parameters {
        string(
            name: 'COMMIT_ID',
            description: 'Docker image tag (git commit ID) to deploy'
        )
    }

    stages {

        stage("Checkout Repository") {
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/main']],
                    doGenerateSubmoduleConfigurations: false,
                    extensions: [[$class: 'RelativeTargetDirectory', relativeTargetDir: '.']],
                    userRemoteConfigs: [[url: 'https://github.com/jisna-testingmavens/infra-repo.git']]
                ])
            }
        }

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
                script {
                    def chartPath = "helm/microservice"
                    if (!fileExists("${chartPath}/Chart.yaml")) {
                        error "Helm chart not found! Checked path: ${chartPath}"
                    } else {
                        echo "Helm chart found at ${chartPath}"
                        env.HELM_CHART_PATH = chartPath
                    }
                }
            }
        }

        stage("Deploy Microservices") {
            steps {
                script {
                    def chartPath = env.HELM_CHART_PATH
                    def services = ["microservice-1", "microservice-2", "microservice-3"]
                    for (svc in services) {
                        echo "Deploying ${svc} with image tag ${params.COMMIT_ID}..."
                        sh "helm upgrade --install ${svc} ${chartPath} --set image.tag=${params.COMMIT_ID}"
                    }
                }
            }
        }
    }

    post {
        success {
            echo "All microservices deployed successfully with image tag ${params.COMMIT_ID}."
        }
        failure {
            echo " Deployment failed. Check logs above for details."
        }
    }
}
