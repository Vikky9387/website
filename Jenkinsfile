pipeline {
    agent any
    stages {

        stage('Build') {
            steps { sh 'docker build -t abodeapp .' }
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
        expression {
            env.GIT_BRANCH == 'origin/master' || env.BRANCH_NAME == 'master'
        }
    }
    steps {
        sh '''
        docker save abodeapp:latest > abodeapp.tar
        scp abodeapp.tar ubuntu@10.0.5.125:/home/ubuntu/

        ssh ubuntu@10.0.5.125 "docker load < /home/ubuntu/abodeapp.tar"
        ssh ubuntu@10.0.5.125 "docker rm -f prodapp || true"
        ssh ubuntu@10.0.5.125 "docker run -d --name prodapp -p 80:80 abodeapp"
        '''
    }
}

