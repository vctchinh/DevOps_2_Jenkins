pipeline {
    agent any

    environment {
        // Đảm bảo Jenkins tìm thấy các lệnh ruff, pytest sau khi cài đặt
        PATH = "$HOME/.local/bin:${env.PATH}"
    }

    stages {
        stage('Checkout') {
            steps {
                // Tương đương bước checkout scm trong file cũ [cite: 1]
                checkout scm
            }
        }

        stage('Environment Setup') {
            steps {
                sh '''
                    # Cập nhật pip và cài đặt các công cụ cần thiết vào thư mục user
                    python3 -m pip install --upgrade pip --user
                    python3 -m pip install --user ruff pytest pytest-cov coverage
                '''
            }
        }

        stage('Lint with Ruff') {
            steps {
                script {
                    // Sử dụng catchError để Pipeline không dừng lại nếu có lỗi định dạng code [cite: 4, 5]
                    catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                        sh 'python3 -m ruff check .'
                    }
                }
            }
        }

        stage('Run Unit Tests') {
            steps {
                // Chạy test cho file test_main.py và đo độ bao phủ (coverage) [cite: 6]
                sh 'python3 -m pytest --cov=. --cov-report=term-missing test_main.py'
            }
        }

        stage('Generate Coverage Report') {
            steps {
                // Xuất báo cáo coverage để Jenkins có thể hiển thị nếu cần [cite: 7]
                sh 'python3 -m coverage report -m'
            }
        }
    }

    post {
        always {
            echo 'Quy trình CI hoàn tất.'
        }
    }
}