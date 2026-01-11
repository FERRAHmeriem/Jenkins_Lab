pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

       stage('La phase Test') {
            steps {
                script {
                    try {
                        // 1. Lancement des tests
                        bat './gradlew clean test jacocoTestReport'
                    } finally {
                        // 2. Archivage des résultats des tests unitaires (JUnit)
                        junit '**/build/test-results/**/*.xml'

                        // 3. Génération du rapport Cucumber
                        cucumber buildStatus: "null",
                                 jsonReportDirectory: 'reports/',
                                 fileIncludePattern: '**/*.json',
                                 sortingMethod: 'ALPHABETICAL'
                    }
                }
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
                    
                    mail to: 'ferrahmeriemfrrh@gmail.com',
                         subject: "Success: ${env.JOB_NAME}",
                         body: "Build réussi !"
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
                
                mail to: 'ferrahmeriemfrrh@gmail.com',
                         subject: "Failure : ${env.JOB_NAME}",
                     body: "Failure !"
            }
        }
    }
}
