// Jenkins env var reference https://www.jenkins.io/doc/book/pipeline/jenkinsfile/#working-with-your-jenkinsfile

pipeline {
    agent any
    enviroment {
    DockerURL ='352708296901.dkr.ecr.us-east-1.amazonaws.com'    }
    Image ='simple-webserver-rita:${BRANCH_NAME}_${BUILD_NUMBER}'

    stages {
        stage('Build Simple WebServer') {
            when { anyOf { branch "master"; branch "dev" }}
            steps {
                echo 'Building..'
                sh '''
                cd simple_webserver
                aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin ${DockerURL}
                docker build -t simple-webserver-rita .
                docker tag ${Image} ${DockerURL}/${Image}
                docker push ${DockerURL}/${Image}

                # docker build
                '''
            }
        }
        stage('Test') {
            when { changeRequest() }
            steps {
                echo 'Testing..'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
            }
        }
    }
}