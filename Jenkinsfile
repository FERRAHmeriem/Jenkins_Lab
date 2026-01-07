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
                        bat './gradlew sonar'

            }
        }

    }
}