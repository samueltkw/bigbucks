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
            scp -r . ubuntu@192.168.30.10:/var/www/html
            python3 /var/www/html/app.py
        """

            }
        }
    }
}
