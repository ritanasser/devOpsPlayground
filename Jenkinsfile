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
                IMAGE="simple-webserver-rita:${BRANCH_NAME}_${BUILD_NUMBER}"
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
                python -m unittest simple_webserver/tests/test_flask_web.py
                '''
            }
        }
        stage('Deploy - dev') {
            steps {
                echo 'Deploying ....'
            }
        }
        stage('Deploy - prod') {
            steps {
                echo 'Deploying....'
            }
        }

        stage('Provisioning - Dev') {
            when { allOf { branch "dev"; changeset "infra/**/*.tf" } }
            steps {
                echo 'Provisioning....'
                sh 'cd infra/dev'
                sh '''
                terraform init
                terraform apply
                '''
                // copyArtifacts filter: 'infra/dev/terraform.tfstate', projectName: '${JOB_NAME}'
                // archiveArtifacts artifacts: 'infra/dev/terraform.tfstate', onlyIfSuccessful: true

            }
        }


}
post {
        always {
            emailext body: 'A Test EMail', recipientProviders: [[$class: 'DevelopersRecipientProvider'], [$class: 'RequesterRecipientProvider']], subject: 'Test'
        }
    }
}