pipeline {
    agent any

    parameters {
        string(
            name: 'COMMIT_ID',
            description: 'Docker image tag (git commit ID) to deploy'
        )
    }

    environment {
        // Default Helm chart path; will be validated dynamically
        HELM_CHART_PATH = "helm/microservice"
    }

    stages {

        stage("Checkout Repository") {
            steps {
                script {
                    checkout([
                        $class: 'GitSCM',
                        branches: [[name: '*/main']],
                        doGenerateSubmoduleConfigurations: false,
                        extensions: [[$class: 'RelativeTargetDirectory', relativeTargetDir: '.']],
                        userRemoteConfigs: [[url: 'https://github.com/jisna-testingmavens/infra-repo.git']]
                    ])
                }
            }
        }

        stage("List Workspace (Debug)") {
            steps {
                sh '''
                echo "Current workspace: $PWD"
                echo "Files in workspace:"
                ls -l
                echo "Files in helm folder:"
                ls -l helm || echo "No helm folder found"
                '''
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
                    // Adjust path if helm/microservice exists inside a subfolder
                    if (!fileExists("${env.HELM_CHART_PATH}/Chart.yaml")) {
                        // Try prepending repo folder name (in case Jenkins clones into subfolder)
                        def possiblePath = "infra-repo/helm/microservice"
                        if (fileExists("${possiblePath}/Chart.yaml")) {
                            env.HELM_CHART_PATH = possiblePath
                            echo "Adjusted HELM_CHART_PATH to ${env.HELM_CHART_PATH}"
                        } else {
                            error "Helm chart not found! Checked paths:\n- helm/microservice\n- infra-repo/helm/microservice"
                        }
                    } else {
                        echo "Helm chart found at ${env.HELM_CHART_PATH}"
                    }
                }
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
