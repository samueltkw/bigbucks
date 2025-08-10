pipeline {
    agent any

    environment {
        REMOTE_HOST = "ubuntu@192.168.50.10"
        REMOTE_DIR  = "/home/ubuntu/surveillance-app"
        REPO_URL    = "https://github.com/samueltkw/bigbucks.git"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/samueltkw/bigbucks.git'
            }
        }

        stage('Deploy to Monitor') {
            steps {
                sshagent(credentials: ['monitor-ssh']) {
                    sh """
                        ssh -o StrictHostKeyChecking=no ${REMOTE_HOST} '
                            set -e
                            mkdir -p ${REMOTE_DIR}
                            cd ${REMOTE_DIR}

                            # Clone repo if empty, else pull latest
                            if [ ! -d ".git" ]; then
                                git clone ${REPO_URL} .
                            else
                                git fetch origin main
                                git checkout -B main origin/main
                            fi

                            # Create venv if missing
                            if [ ! -d "venv" ]; then
                                python3 -m venv venv
                            fi

                            ./venv/bin/pip install --upgrade pip
                            ./venv/bin/pip install -r requirements.txt

                            # Kill existing Flask process
                            pkill -f "python.*app.py" || true

                            # Start Flask app in background
                            nohup ./venv/bin/python app.py > app.log 2>&1 &
                        '
                    """
                }
            }
        }
    }

    post {
        success {
            echo "Deploy successful"
        }
        failure {
            echo "Deploy failed"
        }
    }
}
