pipeline {
    agent any

    environment {
        REMOTE_HOST = "192.168.50.10"
        REMOTE_USER = "ubuntu"
        REMOTE_DIR  = "/home/ubuntu/surveillance-app"
        SSH_CRED_ID = "monitor-ssh" // Jenkins SSH key credentials ID
        REPO_URL    = "https://github.com/samueltkw/bigbucks.git"
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
                    sh '''
                        ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${REMOTE_HOST} '
                            set -e
                            mkdir -p ${REMOTE_DIR}
                            cd ${REMOTE_DIR}

                            # If empty folder, clone repo
                            if [ ! -d ".git" ]; then
                                git clone ${REPO_URL} .
                            else
                                git pull
                            fi

                            # Always ensure venv exists
                            if [ ! -d "venv" ]; then
                                python3 -m venv venv
                            fi

                            ./venv/bin/pip install --upgrade pip
                            ./venv/bin/pip install -r requirements.txt

                            # Kill any existing Flask dev server
                            pkill -f "python.*app.py" || true

                            # Start Flask app in background
                            nohup ./venv/bin/python app.py > app.log 2>&1 &
                        '
                    '''
                }
            }
        }
    }

    post {
        failure {
            echo "Deploy failed"
        }
        success {
            echo "Deploy succeeded"
        }
    }
}
