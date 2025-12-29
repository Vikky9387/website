pipeline {
    agent any

    stages {

        stage('Build') {
            steps {
                sh 'docker build -t webapp .'
            }
        }

        stage('Test') {
            steps {
                sshagent(['prod-key']) {
                    sh '''
                    ssh ubuntu@98.91.214.102 "docker rm -f webtest || true"
                    ssh ubuntu@98.91.214.102 "docker run -d --name webtest -p 80:80 webapp"
                    '''
                }
            }
        }

        stage('Prod') {
            when { branch 'master' }
            steps {
                sshagent(['prod-key']) {
                    sh '''
                    ssh ubuntu@18.215.233.0 "docker rm -f webprod || true"
                    ssh ubuntu@18.215.233.0 "docker run -d --name webprod -p 80:80 webapp"
                    '''
                }
            }
        }
    }
}

