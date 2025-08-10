pipeline {
    agent any

    environment {
        REPO_URL   = 'https://github.com/samueltkw/bigbucks.git'
        REMOTE_DIR = '/home/ubuntu/surveillance-app'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: "${REPO_URL}"
            }
        }

        stage('Deploy to Monitor') {
            steps {
                sshagent(credentials: ['monitor-ssh']) {
                    sh """
                        ssh -o StrictHostKeyChecking=no ubuntu@192.168.50.10 '
                            set -e
                            mkdir -p ${REMOTE_DIR}
                            cd ${REMOTE_DIR}

                            # Clone repo if empty, else pull latest
                            if [ ! -d ".git" ]; then
                                git clone ${REPO_URL} .
                            else
                                git pull
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
            echo 'Deploy successful'
        }
        failure {
            echo 'Deploy failed'
        }
    }
}
