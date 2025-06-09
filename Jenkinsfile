pipeline {
    agent any

    tools {
        jdk 'JAVA_HOME'
        nodejs 'NodeJS'
    }

    environment {
        SCANNER_HOME = tool 'sonar'
    }

    stages {
        stage("Clean Workspace") {
            steps {
                cleanWs()
            }
        }

        stage("Git Checkout") {
            steps {
                git 'https://github.com/KastroVKiran/Zomato-Project-Kastro.git'
            }
        }

        stage("SonarQube Analysis") {
            steps {
                withSonarQubeEnv('sonar') {
                    sh '''${SCANNER_HOME}/bin/sonar-scanner \
                        -Dsonar.projectName=zomato \
                        -Dsonar.projectKey=zomato'''
                }
            }
        }

        stage("Install NPM Dependencies") {
            steps {
                sh 'npm install'
            }
        }
        
        stage ("Build Docker Image") {
            steps {
                sh "docker build -t zomato ."
            }
        }
        stage ("Tag & Push to DockerHub") {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker') {
                        sh "docker tag zomato yashthedocker/zomato:latest "
                        sh "docker push yashthedocker/zomato:latest "
                    }
                }
                 stage('Deploy to Kubernetes') {
            steps {
                script {
                    withAWS(credentials: 'aws', region: 'ap-south-1') {
                        withKubeConfig(caCertificate: '', 
                                       clusterName: 'EKS_CLOUD', 
                                       contextName: 'arn:aws:eks:ap-south-1:822070281368:cluster/EKS_CLOUD', 
                                       credentialsId: 'k8s', 
                                       namespace: 'default', 
                                       restrictKubeConfigAccess: false, 
                                       serverUrl: 'https://8D865F2BE127EFBA59B6C5A721C24A36.yl4.ap-south-1.eks.amazonaws.com') {
                            sh "kubectl apply -f deployment.yml"
                            sh "kubectl apply -f service.yml"
                            sh "kubectl get service"
                        }
                    }
                }
            }
        }      
    }
}
