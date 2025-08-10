pipeline {
    agent any
    stages {
        stage('Test SSH') {
            steps {
                sshagent(credentials: ['monitor-ssh']) {
                    sh 'ssh -o StrictHostKeyChecking=no ubuntu@192.168.50.10 echo OK'
                }
            }
        }
    }
}
