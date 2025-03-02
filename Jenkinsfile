pipeline {
    agent any

    environment {
        dockerImage = '' // This can be assigned dynamically in the pipeline
        registry = 'ngwaabanjong/abcapp'
        registryCredential = 'Dockerhub-login' // Ensure this matches your Jenkins credentials ID
    }

    tools {
        maven 'maven' // Replace with your Maven installation name in Jenkins
    }

    stages {
        stage('Clone Repository') {
            steps {
                // Clone public repository without credentials
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/main']], // Replace 'main' with your branch
                    userRemoteConfigs: [[
                        url: 'https://github.com/Ngwaabanjong/ABC-Java-App.git' // Replace with your public repository URL
                    ]]
                ])
            }
        }
        stage('Clean') {
            steps {
                sh 'mvn clean'
            }
        }
        stage('Compile') {
            steps {
                sh 'mvn compile'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Package') {
            steps {
                sh 'mvn package'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    // Build Docker image with the given name
                    dockerImage = "${registry}:latest" // Tag the image
                    sh "docker build -t ${dockerImage} ."
                }
            }
        }
        stage('Push Image to Docker Hub') {
            steps {
                script {
                    // Push Docker image to registry
                    docker.withRegistry('', registryCredential) {
                        sh "docker push ${dockerImage}"
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Build completed successfully!'
            archiveArtifacts artifacts: 'target/*.war', fingerprint: true
        }
        failure {
            echo 'Build failed!'
        }
    }
}
