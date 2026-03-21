pipeline {
    agent any
    
    environment {
        DOCKER_REGISTRY = 'docker.io'
        APP_NAME = 'backend-api'
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Build') {
            steps {
                sh 'npm install'
                sh 'npm run build'
            }
        }
        
        stage('Test') {
            steps {
                sh 'npm test'
            }
        }
        
        stage('Security Scan') {
            steps {
                sh 'npm audit'
            }
        }
        
        stage('Docker Build') {
            steps {
                sh "docker build -t ${APP_NAME}:${BUILD_NUMBER} ."
                sh "docker tag ${APP_NAME}:${BUILD_NUMBER} ${APP_NAME}:latest"
            }
        }
        
        stage('Deploy to Staging') {
            steps {
                sh 'kubectl apply -f k8s/staging/'
            }
        }
        
        stage('Integration Tests') {
            steps {
                sh 'npm run test:integration'
            }
        }
        
        stage('Deploy to Production') {
            when {
                branch 'main'
            }
            steps {
                sh 'kubectl apply -f k8s/production/'
                sh 'kubectl rollout status deployment/${APP_NAME}'
            }
        }
    }
    
    post {
        always {
            cleanWs()
        }
        success {
            slackSend color: 'good', message: "Build ${BUILD_NUMBER} succeeded"
        }
        failure {
            slackSend color: 'danger', message: "Build ${BUILD_NUMBER} failed"
        }
    }
}
