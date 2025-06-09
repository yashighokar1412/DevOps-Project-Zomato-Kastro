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
        
        stage("Build Docker Image") {
            steps {
                sh "docker build -t zomato ."
            }
        }

        stage("Tag & Push to DockerHub") {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker') {
                        sh "docker tag zomato yashthedocker/zomato:latest"
                        sh "docker push yashthedocker/zomato:latest"
                    }
                }
                
            }
        }

        stage("Deploy to Kubernetes") {
    steps {
        script {
            sh '''
                aws eks update-kubeconfig --region ap-south-1 --name EKS_CLOUD
                kubectl apply -f deployment.yml
                kubectl apply -f service.yml
                kubectl get service
            '''
                        }
                    }
                }
            }
        }
