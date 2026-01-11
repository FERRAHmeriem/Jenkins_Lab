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
                withSonarQubeEnv('SonarQube') {
                    bat './gradlew sonar'
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Build & Archive') {
            steps {
                script {
                    bat './gradlew jar generateJavadoc'
                    archiveArtifacts artifacts: 'build/libs/*.jar', fingerprint: true
                    archiveArtifacts artifacts: 'build/docs/javadoc/**/*', fingerprint: true
                }
            }
        }

        stage('Deploy') {
            steps {
                bat './gradlew publish'
            }
        }

        stage('Notification') {
            steps {
                script {
                    // This stage runs after Deploy. If we reached here, it's a success.
                    slackSend(color: '#00FF00', 
                              message: "✅ Déploiement réussi ! Le projet ${env.JOB_NAME} (build #${env.BUILD_NUMBER}) est disponible sur MyMavenRepo.")
                    
                    emailext(to: 'ferrahmeriemfrrh@gmail.com',
                             from: 'mm_ferrah@esi.dz',
                             subject: "SUCCESS: Project ${env.JOB_NAME} - Build #${env.BUILD_NUMBER}",
                             body: "Le pipeline s'est terminé avec succès. Voir les détails ici : ${env.BUILD_URL}")
                }
            }
        }
    }

    post {
        failure {
            // This catches failures from ANY stage above
            script {
                slackSend(color: '#FF0000', 
                          message: "❌ ÉCHEC du pipeline : ${env.JOB_NAME} (build #${env.BUILD_NUMBER}).")
                
                emailext(to: 'ferrahmeriemfrrh@gmail.com',
                         from: 'mm_ferrah@esi.dz',
                         subject: "FAILURE: Project ${env.JOB_NAME} - Build #${env.BUILD_NUMBER}",
                         body: "Une erreur est survenue pendant le pipeline. Veuillez vérifier les logs : ${env.BUILD_URL}")
            }
        }
    }
}
