pipeline {
    agent any
    tools {
        jdk 'JAVA_HOME'
        nodejs 'NodeJS'
    }
    environment {
        SCANNER_HOME=tool 'sonar'
    }
    stages {
        stage ("clean workspace") {
            steps {
                cleanWs()
            }
        }
        stage ("Git Checkout") {
            steps {
                git 'https://github.com/KastroVKiran/Zomato-Project-Kastro.git'
            }
        }
    }
}
