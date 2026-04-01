pipeline {
    agent any // Chạy trực tiếp trên môi trường của server Jenkins

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                // Sử dụng python3 thay vì python nếu server dùng Linux
                sh '''
                    python3 -m pip install --upgrade pip
                    if [ -f requirements.txt ]; then python3 -m pip install -r requirements.txt; fi
                    python3 -m pip install ruff pytest coverage
                '''
            }
        }

        stage('Lint with Ruff') {
            steps {
                script {
                    catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                        sh 'python3 -m ruff check --format=text --target-version=py310 .'
                    }
                }
            }
        }

        stage('Test with Pytest') {
            steps {
                // Đảm bảo chạy pytest thông qua python3 -m để tránh lỗi command not found
                sh 'python3 -m coverage run -m pytest -v -s'
            }
        }

        stage('Generate Coverage Report') {
            steps {
                sh 'python3 -m coverage report -m'
            }
        }
    }
}