pipeline {
    agent any

    environment {
        REMOTE_USER = "ubuntu"
        REMOTE_HOST = "192.168.50.10"
        REMOTE_DIR  = "/home/ubuntu/surveillance-app"
        REPO_URL    = "https://github.com/samueltkw/bigbucks.git"
        SSH_CRED_ID = "ubuntu" // Jenkins credential ID for SSH key
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: "${REPO_URL}"
            }
        }

        stage('Deploy to Monitor') {
            steps {
                sshagent([SSH_CRED_ID]) {
                    sh """
                        ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${REMOTE_HOST} '
                            set -e
                            mkdir -p ${REMOTE_DIR}
                            cd ${REMOTE_DIR}

                            if [ ! -d ".git" ]; then
                                git clone ${REPO_URL} .
                            else
                                git pull
                            fi

                            if [ ! -d "venv" ]; then
                                python3 -m venv venv
                            fi

                            ./venv/bin/pip install --upgrade pip
                            ./venv/bin/pip install -r requirements.txt

                            pkill -f "python.*app.py" || true
                            nohup ./venv/bin/python app.py > app.log 2>&1 &
                        '
                    """
                }
            }
        }
    }

    post {
        success {
            echo "Deploy succeeded"
        }
        failure {
            echo "Deploy f
