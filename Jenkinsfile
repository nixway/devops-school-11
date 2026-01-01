pipeline {
    agent {
        docker {
            image 'nihi1ist/builder:0.1-noble'
        }
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/nixway/test-war-app.git']])
            }
            
        }
        stage('Build project') {
            steps {
                sh 'mvn clean package'
            }
        }
        stage('Build docker image') {
            steps {
                sshagent(credentials: ['test-without-passwd']) {
                    sh '''ssh-keyscan -H jenkins.nixway.org >> ~/.ssh/known_hosts
                    scp nihi1ist@jenkins.nixway.org:~/docker-images/testwebpage/Dockerfile ./
                    docker build -t nihi1ist/mytestwebpage:0.3 .
                    docker push nihi1ist/mytestwebpage:0.3'''
                }
            }
        }
        stage('Deploy project in prod') {
            steps {
                sshagent(credentials: ['test-without-passwd']) {
                    sh '''ssh nihi1ist@jenkins.nixway.org << EOF
cd ~/docker-images/testwebpage
docker pull nihi1ist/mytestwebpage:0.3
docker compose up -d
EOF'''
                }
            }
        }
    }
}
