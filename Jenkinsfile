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
                    # 1. Tạo môi trường ảo (venv) nếu chưa tồn tại
                    # Nếu lệnh venv lỗi, có thể server thiếu gói python3-venv
                    python3 -m venv venv
                    
                    # 2. Kích hoạt venv và cài đặt các công cụ
                    ./venv/bin/python3 -m pip install --upgrade pip
                    ./venv/bin/python3 -m pip install ruff pytest pytest-cov coverage
                '''
            }
        }

        stage('Lint with Ruff') {
            steps {
                script {
                    catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                        # Sử dụng python trong venv để chạy ruff
                        sh './venv/bin/python3 -m ruff check .'
                    }
                }
            }
        }

        stage('Run Unit Tests') {
            steps {
                # Chạy test thông qua python của venv
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
            # Tùy chọn: Xóa venv sau khi build để tiết kiệm dung lượng
            # sh 'rm -rf venv'
        }
    }
}