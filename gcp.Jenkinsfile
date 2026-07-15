environment {
    GCP_PROJECT_ID     = 'your-project-id'
    GKE_CLUSTER_NAME   = 'your-gke-cluster'
    GKE_CLUSTER_REGION = 'us-east4'
    ARTIFACT_REGISTRY  = 'us-east4-docker.pkg.dev'

    // Jenkins Secret File credential (Google Cloud service account JSON)
    GCP_SA_KEY = credentials('gcp-devops-sa')
}

stages {
    stage('Configure GCP & Deploy') {
        steps {
            sh '''
                set -e

                echo "Authenticating with Google Cloud..."
                gcloud auth activate-service-account \
                    --key-file="$GCP_SA_KEY"

                echo "Setting project..."
                gcloud config set project "$GCP_PROJECT_ID"

                echo "Configuring Docker authentication..."
                gcloud auth configure-docker "$ARTIFACT_REGISTRY" --quiet

                echo "Fetching GKE credentials..."
                gcloud container clusters get-credentials \
                    "$GKE_CLUSTER_NAME" \
                    --region "$GKE_CLUSTER_REGION"

                echo "Applying Kubernetes manifests..."
                kubectl apply -f /root/deploy.yaml

                echo "Deployment completed successfully."
            '''
        }
    }
}

This stage will:

Authenticate to Google Cloud using the Jenkins credential.
Set the active GCP project.
Configure Docker to push/pull from Artifact Registry.
Connect to your GKE cluster.
Apply the Kubernetes manifest located at /root/deploy.yaml.
