pipeline {
    agent any
    tools {
        nodejs 'v20.9.0'
    }
  
    options {
        retry(2)
        timestamps()
    }
  
    stages {
        stage('SCM') {
            steps {
                git branch: 'master', url: 'https://github.com/rahulactive/angular_code.git'
            }
        }
        stage('Install Deps') {
            steps {
                sh 'npm install'
            }
        }
        stage('Build') {
            steps {
                sh 'ng build'
            }
            post {
                success {
                    fingerprint 'dist/keyshell'
                }
            }
        }
        stage('Deploy to Nginx') {
            steps {
                sh 'sudo chown -R jenkins:jenkins /var/www/html
                    rm -rf /var/www/html/*'
                sh 'cp -rv dist/keyshell/* /var/www/html/'
            }
        }
        stage('Archive Artifacts') {
            steps {
                sh '''
                  zip -r archive.zip dist/keyshell/*
                '''
                archiveArtifacts artifacts: 'archive.zip', fingerprint: true, onlyIfSuccessful: true
            }
        }
    }
}
