pipeline {

    agent any

    environment {
        // Google Cloud
        GCP_PROJECT_ID     = 'experience-production-430905'
        GKE_CLUSTER_NAME   = 'experience-gke-production'
        GKE_CLUSTER_REGION = 'us-east4'
        ARTIFACT_REGISTRY  = 'us-east4-docker.pkg.dev'

        // Jenkins Secret File credential (Google Service Account JSON)
        GCP_SA_KEY = credentials('gcp-devops-sa')
    }

    stages {

        stage('Configure GCP') {
            steps {
                sh '''
                    set -euo pipefail

                    echo "Authenticating with Google Cloud..."
                    gcloud auth activate-service-account \
                        --key-file="$GCP_SA_KEY"

                    echo "Setting GCP project..."
                    gcloud config set project "$GCP_PROJECT_ID"

                    echo "Configuring Docker authentication..."
                    gcloud auth configure-docker "$ARTIFACT_REGISTRY" --quiet

                    echo "Getting GKE credentials..."
                    gcloud container clusters get-credentials \
                        "$GKE_CLUSTER_NAME" \
                        --region "$GKE_CLUSTER_REGION"

                    echo "Connected to cluster:"
                    kubectl cluster-info
                '''
            }
        }

        stage('Deploy to GKE') {
            steps {
                sh '''
                    set -euo pipefail

                    echo "Applying Kubernetes manifests..."
                    kubectl apply -f /root/deploy.yaml

                    echo "Waiting for deployment rollout..."
                    kubectl rollout status deployment/<deployment-name>

                    echo "Deployment completed successfully."
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Production deployment completed successfully.'
        }

        failure {
            echo '❌ Production deployment failed.'
        }

        always {
            echo 'Pipeline execution finished.'
        }
    }
}
