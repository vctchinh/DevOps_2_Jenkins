pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                // Tải mã nguồn từ GitHub [cite: 1]
                checkout scm
            }
        }

        stage('Environment Setup') {
            steps {
                sh '''
                    # Tạo môi trường ảo và cài đặt thư viện
                    python3 -m venv venv
                    ./venv/bin/python3 -m pip install --upgrade pip
                    ./venv/bin/python3 -m pip install ruff pytest pytest-cov coverage
                '''
            }
        }

        stage('Lint with Ruff') {
            steps {
                // Đã loại bỏ catchError. Nếu Ruff tìm thấy lỗi format, Pipeline sẽ dừng tại đây.
                sh './venv/bin/python3 -m ruff check .'
            }
        }

        stage('Run Unit Tests') {
            steps {
                // Chạy unit test từ file test_main.py [cite: 6]
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
        failure {
            echo 'Pipeline dừng lại do có lỗi xảy ra trong các bước trên.'
        }
    }
}