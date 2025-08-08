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
                sh '''
                sudo cp -r * /var/www/flask-app/
                sudo systemctl restart flask-surveillance
                '''
            }
        }
    }
}
