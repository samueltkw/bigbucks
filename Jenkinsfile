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
