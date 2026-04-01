pipeline {
    agent {
        // Sử dụng Docker để đảm bảo môi trường đồng nhất như ubuntu-latest
        docker { 
            image 'python:3.10' 
        }
    }

    environment {
        // Thiết lập biến môi trường nếu cần
        PYTHONDONTWRITEBYTECODE = '1'
    }

    stages {
        stage('Checkout') {
            steps {
                // Tương đương với actions/checkout@v3
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                    python -m pip install --upgrade pip
                    if [ -f requirements.txt ]; then pip install -r requirements.txt; fi
                    pip install ruff pytest coverage
                '''
            }
        }

        stage('Lint with Ruff') {
            steps {
                script {
                    // catchError cho phép pipeline tiếp tục nếu lint lỗi (tương đương continue-on-error)
                    catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                        sh 'ruff --format=text --target-version=py310 .'
                    }
                }
            }
        }

        stage('Test with Pytest') {
            steps {
                sh 'coverage run -m pytest -v -s'
            }
        }

        stage('Generate Coverage Report') {
            steps {
                sh 'coverage report -m'
            }
        }
    }

    post {
        always {
            // Dọn dẹp hoặc lưu trữ kết quả test
            echo 'Pipeline finished.'
        }
        failure {
            echo 'Build failed. Please check the logs.'
        }
    }
}