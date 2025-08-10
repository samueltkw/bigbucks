pipeline {
    agent any

    environment {
        // Set to "local" if Jenkins is on the same server as the app
        DEPLOY_MODE = "remote"  // options: local or remote

        // Remote deploy settings
        REMOTE_USER = "ubuntu"
        REMOTE_HOST = "192.168.50.10"
        REMOTE_DIR = "/var/www/flask-app"
        SERVICE_NAME = "flask-surveillance"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/samueltkw/bigbucks.git'
                echo 'Code checked out successfully.'
            }
        }

        stage('Deploy') {
            steps {
                script {
                    if (DEPLOY_MODE == "local") {
                        // Local deployment
                        sh '''
                        sudo mkdir -p ${REMOTE_DIR}
                        sudo cp -r app.py requirements.txt templates static ${REMOTE_DIR}/
                        sudo ${REMOTE_DIR}/venv/bin/pip install -r ${REMOTE_DIR}/requirements.txt
                        sudo systemctl restart ${SERVICE_NAME}
                        '''
                    } else {
                        // Remote deployment
                        sshagent(['monitor-ssh']) {
                            sh '''
                            ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${REMOTE_HOST} "
                                set -e
                                sudo mkdir -p ${REMOTE_DIR}
                                cd ${REMOTE_DIR}
                                if [ ! -d venv ]; then
                                    python3 -m venv venv
                                fi
                            "

                            scp -o StrictHostKeyChecking=no \
                                -r app.py requirements.txt templates static \
                                ${REMOTE_USER}@${REMOTE_HOST}:${REMOTE_DIR}/

                            ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${REMOTE_HOST} "
                                ${REMOTE_DIR}/venv/bin/pip install -r ${REMOTE_DIR}/requirements.txt
                                sudo systemctl restart ${SERVICE_NAME}
                            "
                            '''
                        }
                    }
                }
            }
        }
    }
}
