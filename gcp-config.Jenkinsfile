pipeline {
    agent any

    environment {
        GCP_SA_KEY = credentials('gcpkey')
        GCP_PROJECT_ID = "ajith-gcp"
        GKE_CLUSTER_NAME = "ajith-cluster"
        GKE_CLUSTER_REGION = "us-central1"
        ARTIFACT_REGISTRY = "us-central1-docker.pkg.dev/ajith-gcp/ajith-repo/java:latest"
    }

    stages {
        stage('GCP Config') {
            steps {
                sh '''
                    set -euo pipefail

                    echo "Authenticating with GCP..."
                    gcloud auth activate-service-account \
                        --key-file="$GCP_SA_KEY"

                    echo "Setting GCP project..."
                    gcloud config set project "$GCP_PROJECT_ID"

                    echo "Setting GCP region..."
                    gcloud config set compute/region "$GKE_CLUSTER_REGION"

                    echo "Configuring Docker authentication..."
                    gcloud auth configure-docker \
                        us-central1-docker.pkg.dev \
                        --quiet

                    echo "Getting GKE credentials..."
                    gcloud container clusters get-credentials \
                        "$GKE_CLUSTER_NAME" \
                        --region "$GKE_CLUSTER_REGION" \
                        --project "$GCP_PROJECT_ID"

                    echo "Testing Kubernetes connection..."
                    kubectl cluster-info
                '''
            }
        }
    }
}
