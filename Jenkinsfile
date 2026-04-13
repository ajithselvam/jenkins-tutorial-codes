pipeline {
    agent any

    environment {
        // Define variables for your tools and registry
        DOCKER_IMAGE = "my-app"
        DOCKER_TAG = "${env.BUILD_NUMBER}"
        REGISTRY_CREDENTIALS = "docker-hub-credentials-id"
    }

    stages {
        stage('1. Code & Checkout') {
            steps {
                // Pulls code from the GitHub repository configured in the job
                checkout scm
                echo "Code checked out successfully."
            }
        }

        stage('2. Build') {
            steps {
                echo "Compiling and resolving dependencies..."
                // Example for a Java/Maven project:
                // sh 'mvn clean compile'
            }
        }

        stage('3. Test') {
            parallel {
                stage('Unit & Integration Tests') {
                    steps {
                        echo "Running Selenium and Unit tests..."
                        // sh 'mvn test'
                    }
                }
                stage('Static Code Analysis') {
                    steps {
                        echo "Running SonarQube analysis..."
                        // withSonarQubeEnv('SonarQube') { sh 'mvn sonar:sonar' }
                    }
                }
            }
        }

        stage('4. Package') {
            steps {
                echo "Building Docker image..."
                script {
                    dockerImage = docker.build("${DOCKER_IMAGE}:${DOCKER_TAG}")
                }
            }
        }

        stage('5. Deploy') {
            steps {
                echo "Deploying to Kubernetes..."
                // Example using kubectl to update a deployment
                // sh "kubectl set image deployment/my-app-deployment my-app=${DOCKER_IMAGE}:${DOCKER_TAG}"
            }
        }
    }

    post {
        always {
            echo "Cleaning up workspace..."
            cleanWs()
        }
        success {
            echo "Pipeline completed successfully! Monitoring via Prometheus/Grafana."
        }
        failure {
            echo "Pipeline failed. Check logs for details."
        }
    }
}

