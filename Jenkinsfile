pipeline {
    agent any
    stages {
        stage('Test SSH Agent') {
            steps {
                sshagent(credentials: ['ubuntu']) {
                    sh 'echo test'
                }
            }
        }
    }
}
