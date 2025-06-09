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

        stage("OWASP FS Scan") {
            steps {
                dependencyCheck additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit -n', odcInstallation: 'DP-Check'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
    }
}
