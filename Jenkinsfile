
pipeline {

    agent any

    stages {

        stage('Build') {
            steps {
                echo 'Setting up Python environment'
                sh '''
                    python3 -m venv .venv
                    . .venv/bin/activate
                    pip install -r requirements.txt --quiet
                '''
            }
        }

        stage('Test') {
            steps {
                echo 'Running unit tests'
                sh '''
                    . .venv/bin/activate
                    python -m pytest tests/ --junitxml=test-results.xml -v
                '''
            }
            post {
                always {
                    junit 'test-results.xml'
                }
            }
        }

        stage('Package') {
            steps {
                echo 'Creating artifact...'
                sh 'echo "Build=${BUILD_NUMBER}" > build-info.txt'
                archiveArtifacts artifacts: 'build-info.txt'
            }
        }
    }

    post {
        success { echo 'Pipeline succeeded!' }
        failure { echo 'Pipeline failed' }
    }
}