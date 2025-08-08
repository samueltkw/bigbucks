pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Cloning source code...'
                git branch: 'main', url: 'https://github.com/samueltkw/bigbucks.git'
            }
        }
        stage('Test') {
            steps {
                echo 'Simulating tests...'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying to monitor server...'
                sh '''
                ssh ubuntu@192.168.30.10 "pkill -f app.py || true"
                scp -r * ubuntu@192.168.30.10:/home/ubuntu/
                ssh ubuntu@192.168.30.10 "nohup python3 /home/ubuntu/app.py > /home/ubuntu/app.log 2>&1 &"
                '''
            }
        }
    }
}
