pipeline {
    agent any
    tools {
        nodejs 'nodejs20.19.0'
    }

    options {
        retry(2)
        timestamps()
    }

    environment {
        DOCKER_IMAGE = 'angular-nginx-app'
        DOCKER_CONTAINER = 'angular-container'
    }

    stages {
        stage('SCM') {
            steps {
                git branch: 'main', url: 'https://github.com/rahulactive/angular_code.git'
            }
        }

        stage('Install Deps') {
            steps {
                sh 'npm install'
            }
        }

        stage('Build Angular') {
            steps {
                sh 'npm run build'
            }
            post {
                success {
                    fingerprint 'dist/keyshell'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Deploy with Docker') {
            steps {
                sh '''
                    docker rm -f $DOCKER_CONTAINER || true
                    docker run -d --name $DOCKER_CONTAINER -p 8070:80 $DOCKER_IMAGE
                '''
            }
        }

        stage('Archive Artifacts') {
            steps {
                sh 'zip -r archive.zip dist/keyshell/*'
                archiveArtifacts artifacts: 'archive.zip', fingerprint: true, onlyIfSuccessful: true
            }
        }
    }
}

