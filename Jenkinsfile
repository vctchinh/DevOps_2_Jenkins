pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Environment Setup') {
            steps {
                sh '''
                    python3 -m venv venv
                    ./venv/bin/python3 -m pip install --upgrade pip
                    ./venv/bin/python3 -m pip install ruff pytest pytest-cov coverage
                '''
            }
        }

        stage('Lint with Ruff') {
            steps {
                script {
                    catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                        sh './venv/bin/python3 -m ruff check .'
                    }
                }
            }
        }

        stage('Run Unit Tests') {
            steps {
                sh './venv/bin/python3 -m pytest --cov=. --cov-report=term-missing test_main.py'
            }
        }

        stage('Generate Coverage Report') {
            steps {
                sh './venv/bin/python3 -m coverage report -m'
            }
        }
    }

    post {
        always {
            echo 'Quy trình CI đã hoàn tất.'
        }
    }
}