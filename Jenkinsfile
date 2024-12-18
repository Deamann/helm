pipeline {
    agent any 

    stages {
        stage('Initialize Helm') {
            steps {
                echo 'Initializing Helm...'
                sh 'helm version'
                sh 'kubectl cluster-info' // Ensure Kubernetes cluster is reachable
                sh 'helm ls'
            }
        }

        stage('Lint Helm Chart') {
            steps {
                echo 'Linting Helm Chart...'
                sh 'helm lint ./helm/serviceapp --strict' // Strict validation for better chart quality
            }
        }

        stage('Deploy Application') {
            steps {
                echo 'Deploying Application with Helm...'
                sh '''
                helm upgrade --install test-helm ./helm/serviceapp --namespace default
                '''
            }
        }

        stage('Verify Deployment and Helm List') {
            steps {
                echo 'Verifying Deployment...'
                sh 'helm ls'
                sh 'kubectl get pods -o wide' // More detailed information
                sh 'kubectl rollout status deployment/<deployment-name>' // Checks deployment readiness
            }
        }
    }

    post {
        success {
            echo 'Deployment Successful'
        }
        failure {
            echo 'Pipeline Failed. Troubleshoot the error!'
            echo 'Cleaning up resources...'
            sh 'helm uninstall test-helm'
        }
    }
}

