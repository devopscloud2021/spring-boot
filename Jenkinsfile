pipeline {
    agent any

    environment {
        registry = "451426294855.dkr.ecr.ap-south-1.amazonaws.com/k8s"
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/devopscloud2021/spring-boot.git']])
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
                    dockerimage = docker.build registry
                    dockerimage.tag("$BUILD_NUMBER")
                }
            }
        }
        
        stage ("Push to ECR") {
            steps {
                script {
                    sh "aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 451426294855.dkr.ecr.ap-south-1.amazonaws.com"
                    sh "docker push docker push 451426294855.dkr.ecr.ap-south-1.amazonaws.com/k8s:$BUILD_NUMBER"
                    
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
