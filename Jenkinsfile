pipeline {
    agent any
    stages {
        stage('Git Clone') {
            steps {
                git url: 'https://github.com/santu-365/php-project', branch: "master"
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t santu365/php-app:v1 .'
                    sh 'docker images'
                }
            }
        }
        stage('Docker Login & Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-pwd', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                    sh 'echo $PASS | docker login -u $USER --password-stdin'
                    sh 'docker push santu365/php-app:v1'
                }
            }
        }
        stage('Deploy') {
  steps {
    sshagent(['sshkeypair']) {
      sh '''
        ssh -o StrictHostKeyChecking=no ubuntu@44.221.51.235 '
        sudo docker rm -f php-app-container || true &&
        sudo docker pull santu365/php-app:v1 &&
        sudo docker run -d -p 80:80 --name php-app-container santu365/php-app:v1
        '
      '''
    }
  }
}

