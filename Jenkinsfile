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
                bat './gradlew clean test jacocoTestReport'
            }
        }

        stage('Code Analysis') {
            steps {
                // Le wrapper 'withSonarQubeEnv' prépare les variables d'environnement
                // 'SonarQube' est le nom configuré dans Administrer Jenkins > System
                withSonarQubeEnv('SonarQube') {
                    bat './gradlew sonar'
                }
            }
        }

        stage('Quality Gate') {
            steps {
                // Maintenant Jenkins sait quelle analyse attendre
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }
}
