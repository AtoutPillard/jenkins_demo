pipeline {
    agent any
    options {
        timeout(time: 10, unit: 'MINUTES') 
        timestamps()                          
    }

    parameters {
        choice(name: 'ENVIRONNEMENT', choices: ['staging', 'production'],
               description: 'Cible de déploiement')
        booleanParam(name: 'LANCER_TESTS', defaultValue: true,
               description: 'Exécuter les tests ?')
    }

    environment {
        APP_NAME = 'calculatrice-demo'
    }

    stages {

        stage('Checkout') {
            steps {
                echo "Récupération du code de ${APP_NAME}..."
            }
        }

        stage('Build') {
            steps {
                echo "Build de ${APP_NAME} (build n° ${env.BUILD_NUMBER})"
                sh 'python3 --version'
                sh 'pip install -r requirements.txt --quiet || true'
            }
        }

        stage('Test') {
            when { expression { return params.LANCER_TESTS } }
            steps {
                echo 'Lancement des tests unitaires...'
                sh 'python3 -m pytest tests/ --junitxml=resultats-tests.xml -v'
            }
            post {
                always {
                    junit 'resultats-tests.xml'
                }
            }
        }
        stage('Vérifications qualité') {
            parallel {
                stage('Lint') {
                    steps {
                        echo 'Analyse statique du code (simulée)...'
                        sh 'echo "OK : style conforme"'
                    }
                }
                stage('Sécurité') {
                    steps {
                        echo 'Scan de sécurité (simulé)...'
                        sh 'echo "OK : aucune vulnérabilité"'
                    }
                }
            }
        }

        stage('Package') {
            steps {
                echo 'Création de l\'artefact...'
                sh 'echo "Version=${BUILD_NUMBER}" > build-info.txt'
                sh 'cat build-info.txt'
            }
            post {
                success {
                    archiveArtifacts artifacts: 'build-info.txt', fingerprint: true
                }
            }
        }
        stage('Deploy') {
            when { expression { return params.ENVIRONNEMENT == 'production' } }
            steps {
                input message: "Déployer en PRODUCTION ?", ok: 'Déployer'
                echo "Déploiement de ${APP_NAME} en ${params.ENVIRONNEMENT}..."
            }
        }
    }
    post {
        success {
            echo "Pipeline terminé avec succès (build n° ${env.BUILD_NUMBER})"
        }
        failure {
            echo "Le pipeline a échoué — regardez le stage en rouge."
        }
        always {
            echo 'Nettoyage terminé.'
        }
    }
}
