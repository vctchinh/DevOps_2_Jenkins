pipeline {
    agent any

    environment {
        // Cấu hình đường dẫn để Jenkins tìm thấy các lệnh python/pytest sau khi cài đặt
        PATH = "$HOME/.local/bin:${env.PATH}"
    }

    stages {
        stage('Checkout') {
            steps {
                // Tải mã nguồn từ GitHub
                checkout scm
            }
        }

        stage('Environment Setup') {
            steps {
                sh '''
                    # 1. Tải script cài đặt pip chính thức từ pypa vì server đang thiếu module pip
                    curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
                    
                    # 2. Chạy script để cài đặt pip vào thư mục cá nhân của user jenkins
                    python3 get-pip.py --user
                    
                    # 3. Sử dụng pip vừa cài để cài đặt ruff, pytest và coverage
                    python3 -m pip install --user ruff pytest pytest-cov coverage
                '''
            }
        }

        stage('Lint with Ruff') {
            steps {
                script {
                    // Kiểm tra lỗi trình bày code nhưng không làm dừng Pipeline nếu có lỗi nhẹ
                    catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                        sh 'python3 -m ruff check .'
                    }
                }
            }
        }

        stage('Run Unit Tests') {
            steps {
                // Chạy các bài test trong file test_main.py và đo độ bao phủ mã nguồn (coverage)
                // Sử dụng python3 -m để đảm bảo gọi đúng module đã cài ở bước Setup
                sh 'python3 -m pytest --cov=. --cov-report=term-missing test_main.py'
            }
        }

        stage('Generate Coverage Report') {
            steps {
                // Hiển thị bảng tổng kết độ bao phủ của các bài test
                sh 'python3 -m coverage report -m'
            }
        }
    }

    post {
        always {
            echo 'Quy trình CI trên Jenkins đã hoàn tất.'
        }
    }
}