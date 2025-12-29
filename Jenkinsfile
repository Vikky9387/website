pipeline {
    agent any

    stages {

        stage('Build') {
            steps {
                sh '''
                docker build -t webapp .
                docker save webapp > webapp.tar
                '''
            }
        }

        stage('Test') {
            steps {
                sshagent(['prod-key']) {
                    sh '''
                    scp webapp.tar ubuntu@98.91.214.102:/home/ubuntu/

                    ssh ubuntu@98.91.214.102 "
                      docker load < /home/ubuntu/webapp.tar &&
                      docker rm -f webtest || true &&
                      docker run -d --name webtest -p 80:80 webapp
                    "
                    '''
                }
            }
        }

        stage('Prod') {
            when { branch 'master' }
            steps {
                sshagent(['prod-key']) {
                    sh '''
                    scp webapp.tar ubuntu@18.215.233.0:/home/ubuntu/

                    ssh ubuntu@18.215.233.0 "
                      docker load < /home/ubuntu/webapp.tar &&
                      docker rm -f webprod || true &&
                      docker run -d --name webprod -p 80:80 webapp
                    "
                    '''
                }
            }
        }
    }
}
