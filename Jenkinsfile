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
                echo 'Deploying to remote server...'
                

                sh '''
                ssh ubuntu@192.168.30.10 'mkdir -p /var/www/flask-app'
                scp -r * ubuntu@192.168.30.10:/var/www/flask-app/
                '''


            }
        }
    }
}
