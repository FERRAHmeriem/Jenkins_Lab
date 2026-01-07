pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build & Test') {
            steps {
                bat './gradlew test'
            }
        }

        stage('Code Analysis') {
            steps {
                script {
                    withSonarQubeEnv('SonarQube') {
                        sh './gradlew sonarqube'
                    }
                }
            }
        }

    }
}