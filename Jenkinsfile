pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                git branch: 'main', url: 'https://github.com/samueltkw/bigbucks.git'
                echo 'Build stage completed.'
            }
        }
        stage('Test') {
            steps {
                echo 'Simulating unit tests...'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying to server...'

        sh """

            rsync -avz --exclude='.git' ./ ubuntu@192.168.30.10:~/bigbucks/

            ssh ubuntu@192.168.30.10 '
                cd ~/bigbucks &&
                python3 app.py
            
        """

            }
        }
    }
}
