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
        stage('Build & Archive') {
            steps {
                script {
                    // 1. Génération du Jar et 2. Génération de la Javadoc
                    // On utilise la tâche 'generateJavadoc' que vous avez définie dans votre build.gradle
                    bat './gradlew jar generateJavadoc'
                    
                    // 3. Archivage du fichier Jar et de la documentation
                    // archiveArtifacts permet de conserver les fichiers dans l'interface Jenkins
                    archiveArtifacts artifacts: 'build/libs/*.jar', fingerprint: true
                    
                    // On archive tout le dossier javadoc pour qu'il soit consultable
                    archiveArtifacts artifacts: 'build/docs/javadoc/**/*', fingerprint: true
                }
            }
        }
    stage('Deploy') {
        steps {
            script {
                // On utilise la tâche 'publish' qui va envoyer le JAR vers le dépôt distant
                // On passe les credentials via les variables d'environnement si nécessaire
                bat './gradlew publish'
            }
        }
}
    }
}
