pipeline {
    agent any

    stages {

        stage('Build') {
            steps {
                sh 'docker build -t abodeapp .'
            }
        }

        stage('Test') {
            steps {
                sh '''
                docker rm -f testapp || true
                docker run -d --name testapp -p 8081:80 abodeapp
                sleep 5
                curl http://localhost:8081
                '''
            }
        }

        stage('Prod') {
            when {
                branch 'master'
            }
            steps {
                sh '''
                ssh ubuntu@10.0.13.166 '
                docker rm -f prodapp || true
                docker run -d --name prodapp -p 80:80 abodeapp
                '
                '''
            }
        }
    }
}
