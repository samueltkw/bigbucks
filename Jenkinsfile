pipeline {
  agent any
  environment {
    TARGET_HOST = '192.168.50.10'              // Monitor server IP
    APP_DIR     = '/home/ubuntu/surveillance-app'
  }
  stages {
    stage('Checkout') {
      steps {
        git url: 'https://github.com/<yourusername>/<your-repo>.git', branch: 'main'
      }
    }
    stage('Build') {
      steps {
        sh 'python3 -V'
        sh 'echo "Static checks placeholder"'
      }
    }
    stage('Test') {
      steps {
        sh 'echo "Run unit tests here (skipped for now)"'
      }
    }
    stage('Deploy') {
      steps {
        sshagent(credentials: ['monitor-ssh']) {
          // Ensure dirs/venv on target
          sh """
            ssh -o StrictHostKeyChecking=yes ubuntu@${TARGET_HOST} \\
              'mkdir -p ${APP_DIR} && python3 -m venv ${APP_DIR}/venv || true'
          """
          // Sync code
          sh """
            rsync -az --delete \\
              --rsh='ssh -o StrictHostKeyChecking=yes' \\
              ./ ubuntu@${TARGET_HOST}:${APP_DIR}/
          """
          // Install deps and (re)start service
          sh """
            ssh ubuntu@${TARGET_HOST} '
              set -e
              cd ${APP_DIR}
              ${APP_DIR}/venv/bin/pip install --upgrade pip
              ${APP_DIR}/venv/bin/pip install -r requirements.txt
              sudo systemctl restart bigbucks || sudo systemctl start bigbucks
              sudo systemctl is-active --quiet bigbucks
            '
          """
        }
      }
    }
  }
  post {
    success { echo 'Deploy OK' }
    failure { echo 'Deploy failed' }
  }
}
