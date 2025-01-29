pipeline {
    agent any
    tools {
        nodejs 'mynode'
    }
 
    stages {
        stage('git cloning') {
            steps {
                echo 'cloning files from github'
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/Divyap8/nodepipeline.git']])
            }
        }
        stage('Build') {
            steps {
                echo 'Building nodejs project'
                sh 'npm install'
            }
        }
        stage('Test') {
            steps {
                echo 'Testing project'
                sh './node_modules/mocha/bin/_mocha --exit ./test/test.js'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying nodejs project on live server'
                script{
                    sshagent(['397b7adf-a0b1-441f-8a3b-a3b1c223d50c']) {
                     sh '''
                     ssh -o StrictHostKeyChecking=no ec2-user@18.143.73.159<<EOF
                     cd /home/ec2-user/nodejs
                     git pull https://github.com/Divyap8/nodepipeline.git
                     npm install
                     sudo npm install -g pm2
                     pm2 restart index.js || pm2 start index.js
		             exit
                     EOF     
                    '''
                    }
                }
            }
        }
    }
}
