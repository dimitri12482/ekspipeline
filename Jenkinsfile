pipeline {
    agent any

    environment {
        registry = "731040831586.dkr.ecr.us-west-2.amazonaws.com/cloud-native"
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/dimitri12482/ekspipeline/']])
            }
        }
        
        stage ("Build JAR") {
            steps {
                sh "mvn clean install"
            }
        }
        
        stage ("Build Image") {
            steps {
                script {
                    docker.build registry
                }
            }
        }
        
        stage ("Push to ECR") {
            steps {
                script {
                   sh 'aws ecr get-login-password --region us-west-2 | docker login --username AWS --password-stdin 731040831586.dkr.ecr.us-west-2.amazonaws.com'
                   sh 'docker push 731040831586.dkr.ecr.us-west-2.amazonaws.com/cloud-native:$BUILD_NUMBER'
                    
                }
            }
        }
        
        stage ("Helm package") {
            steps {
                    sh "helm package springboot"
                }
            }
                
        stage ("Helm install") {
            steps {
                    sh "helm upgrade myrelease-21 springboot-0.1.0.tgz"
                }
            }
    }
}
