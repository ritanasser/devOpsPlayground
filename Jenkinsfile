// Jenkins env var reference https://www.jenkins.io/doc/book/pipeline/jenkinsfile/#working-with-your-jenkinsfile

pipeline {
    agent any
    environment {
    DockerURL ='352708296901.dkr.ecr.us-east-1.amazonaws.com'
     }

    stages {
        stage('Build Simple WebServer') {
            when { anyOf { branch "master"; branch "dev" }}
            steps {
                echo 'Building..'
                sh '''
                IMAGE='simple-webserver-rita:${BRANCH_NAME}_${BUILD_NUMBER}'
                cd simple_webserver
                aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin ${DockerURL}
                docker build -t ${IMAGE} .
                docker tag ${IMAGE} ${DockerURL}/${IMAGE}
                docker push ${DockerURL}/${IMAGE}

                # docker build
                '''
            }
        }
        stage('Test') {
            when { changeRequest() }
            steps {
                echo 'Testing..'
                sh '''
                pip3 install -r simple_webserver/requirements.txt
                python3 -m unittest simple_webserver/tests/test_flask_web.py
                '''

            }
        }
        stage('Deploy - dev') {
            steps {
                echo 'Deploying....'
            }
        }
        stage('Deploy - prod') {
            steps {
                echo 'Deploying....'
            }
        }
        stage('Provision') {
            when { changeset "infra/**" }
            input {
                message "Do you want to proceed for infrastructure provisioning?"
            }
            steps {
                // copyArtifacts filter: 'infra/dev/terraform.tfstate', projectName: '${JOB_NAME}'
                echo 'Provisioning....'
                // archiveArtifacts artifacts: 'infra/dev/terraform.tfstate', onlyIfSuccessful: true
            }
        }

    }
}