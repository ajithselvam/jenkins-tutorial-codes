pipeline {
    agent any
    environment{
        DIR = '3-java-jenkins-docker-app'
        IMAGE = 'java-jenkins-docker'
        REPO = 'ajith567890/java-jenkins-docker'
        GCP_REPO = 'REPO = 'us-central1-docker.pkg.dev/PROJECT_ID/REPOSITORY/java-jenkins-docker'
        GCP_SA_KEY = credentials('gcpsakey')
        GCP_PROJECT_ID = 'ajith-gcp-project'
        GKE_CLUSTER_REGION = 'us-central1'
        GKE_CLUSTER_NAME = 'ajith-cluster'
        ARTIFACT_REGISTRY = 'us-central1-docker.pkg.dev'
    }
    
    stages{
        stage('gcp config'){
            steps{
                sh '''
                set -euo pipefail
                gcloud auth activate-service-account --keyfile="$GCP_SA_KEY"
                gcloud config set project "$GCP_PROJECT_ID"
                gcloud auth configure-docker "$ARTIFACT_REGISTRY" --quiet
                gcloud container clusters get-credentials "$GKE_CLUSTER_NAME" --region "$GKE_CLUSTER_REGION"
                kubectl cluster-info
                '''
            }
        }
        stage('checkout'){
            steps{
                git(
                    url: 'https://github.com/ajithselvam/java-jenkins-docker-project.git',
                    branch: 'main',
                    poll: false,
                    changelog: true
                    )
            }
        }
        stage('list'){
            steps{
                dir("${env.DIR}"){
                    sh 'ls'
                }
            }
        }
        stage('maven build'){
            steps{
                dir("${env.DIR}"){
                    sh 'mvn clean package'
                }
            }
        }
        stage('maven test'){
            steps{
                dir("${env.DIR}"){
                    sh 'mvn test'
                }
            }
        }
        stage('maven compile'){
            steps{
                dir("${env.DIR}"){
                    sh 'mvn compile'
                }
            }
        }
        stage('docker image build'){
            steps{
                dir("${env.DIR}"){
                    sh "docker build -t ${env.IMAGE}:latest ."
                }
            }
        }
        stage('docker run container'){
            steps{
                dir("${env.DIR}"){
                    sh "docker run --rm ${env.IMAGE}:latest"
                }
            }
        }
        stage('docker tag'){
            steps{
                dir("${env.DIR}"){
                    sh "docker tag ${IMAGE}:latest ${REPO}:latest"
                }
            }
        }
        stage('docker login'){
            steps{
                withCredentials([usernamePassword(
                    credentialsId: 'docker-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                    )]) {
                        sh '''
                        echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                        '''
                    }
                }
            }
            stage('docker push'){
                steps{
                    sh "docker push ${REPO}:latest"
                }
            }
            
            stage('Deploy') {
            steps {
                sh '''
                kubectl apply -f deployment.yaml
                kubectl apply -f service.yaml
                '''
            }
        }
        
        stage('Verify Deployment') {
            steps {
                sh '''
                kubectl rollout status deployment/myapp
                kubectl get pods
                kubectl get svc
                '''
            }
        }
            stage('all done'){
                steps{
                    sh '''
                    echo "all done at $(date) by $(whoami)"
                    '''
                }
            }
            stage('cleanws'){
                steps{
                    cleanWs()
                }
            }
        }
        post{
            success{
                echo 'succ'
            }
            failure{
                echo 'fail'
            }
            always{
                cleanWs()
            }
        }
    }
