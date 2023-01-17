pipeline {
    agent any
    
    environment {
        dockerImage = ''
    }

    stages {
        stage('Poll SCM') {
            steps {
                
               checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/anoopraj467/Capston']])
            }
        }
        stage('npm install') {

            steps {
                sh 'npm install'
            }
        }
        stage('test') {

            steps {
                sh 'npm run test'
            }
        }
        stage('Sonar'){
            steps {
                sh '''
                    sudo npm install -g sonarqube-scanner
                    sonar-scanner \
                    -Dsonar.projectKey=devnodejs \
                    -Dsonar.sources=. \
                    -Dsonar.host.url=http://21.68.219.122:9000 \
                    -Dsonar.login=sqp_f98c8f509ab60596b3487c125f2b72054d7d3204
                '''
            }
        }
        stage('docker build') {
            steps{
                script{
                    dockerImage = docker.build("anoop47/devops-node:latest")
                }
            }
        }
        stage('docker login and push') {
            steps {
                script {
                    dockerImage.push()
                }
            }
        }
        stage('install docker'){
            steps{
                ansiblePlaybook credentialsId: 'root-host-verification-false', disableHostKeyChecking: true, inventory: 'ansible/dev.inv', playbook: 'ansible/dockerInstall.yml'
            }
        }
        stage('deploy docker image'){
            steps{
                ansiblePlaybook credentialsId: 'root-host-verification-false', disableHostKeyChecking: true, inventory: 'ansible/dev.inv', playbook: 'ansible/deploy.yml'
            }
        }
    }
}