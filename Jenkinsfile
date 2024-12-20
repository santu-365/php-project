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
        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-pwd', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                    sh "echo $PASS | docker login -u $USER --password-stdin"
                    sh 'docker push santu365/php-app:v1'
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    def dockerrm = 'sudo docker rm -f php-app-container || true'
                    def dockerCmd = 'sudo docker run -itd --name php-app-container -p 8083:80 santu365/php-app:v1'
                    sshagent(['sshkeypair']) {
                        sh "ssh -o StrictHostKeyChecking=no ubuntu@<Node-Private-IP> ${dockerrm}"
                        sh "ssh -o StrictHostKeyChecking=no ubuntu@<Node-Private-IP> ${dockerCmd}"
                    }
                }
            }
        }
    }
}

