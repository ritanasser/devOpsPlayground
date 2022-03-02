// Jenkins env var reference https://www.jenkins.io/doc/book/pipeline/jenkinsfile/#working-with-your-jenkinsfile

pipeline {
    agent{ label 'ec2-fleet'}

    environment {
    DockerURL ='352708296901.dkr.ecr.us-east-1.amazonaws.com'
       }

    stages {

        stage('Build Simple WebServer') {
            when { anyOf { branch "master"; branch "dev" }}
            steps {
                echo 'Building..'
                sh '''
                cd simple_webserver




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

        stage('Provisioning - dev') {
            when { changeset "infra/dev/**"  }
             // input {
               //message "Do you want to proceed for infrastructure provisioning?"
            //}
            steps {
                echo 'Provisioning....'
                sh 'cd infra/dev'
                sh '''
                terraform init && terraform paln && terraform apply -auto-approve
                '''
                // copyArtifacts filter: 'infra/dev/terraform.tfstate', projectName: '${JOB_NAME}'
                archiveArtifacts artifacts: 'infra/dev/terraform.tfstate', onlyIfSuccessful: true

            }
        }


}
//post {
        //always {
        //    emailext body: 'A Test EMail', recipientProviders: [[$class: 'DevelopersRecipientProvider'], [$class: 'RequesterRecipientProvider']], subject: 'Test'
      //  }
    //}
}