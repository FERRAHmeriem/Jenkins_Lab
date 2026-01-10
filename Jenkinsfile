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
        stage('Quality Gate') {
                    steps {
                        // Cette étape attend le retour de SonarQube
                        // Le pipeline s'arrête en erreur (Failed) si le Quality Gate échoue
                        timeout(time: 5, unit: 'MINUTES') { 
                            waitForQualityGate abortPipeline: true
                        }
                    }
         }
    }
}
