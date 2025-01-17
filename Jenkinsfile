pipeline {
    agent any
      options {
    ansiColor('xterm')
    }
    environment {
        LAMBDA_TGT_ZIP = "src.zip"
    }
    stages {
        stage("Prepare Lambda Python Package") {
            steps {
                sh 'rm -fr ./dist'
                sh 'pip3 install pipenv'
                sh 'pipenv lock --requirements > ./requirements.txt'
                sh 'pip3 install -r ./requirements.txt -t ./dist'
                sh 'cp my-lambda/src/my-lambda.py ./dist'
                sh(
                '''
                if [ $(du -ms ./dist | cut -f1) -gt 250 ]; then \
                    echo $(du -ms ./dist | cut -f1) exceeds 250MB; \
                    false; \
                fi
                '''
                )
            }
        }
        stage("Zip Lambda Package") {
            steps {
                sh 'cd ./dist && zip -r ${LAMBDA_TGT_ZIP} .'
                sh(
                '''
                if [ $(du -ms ./dist/${LAMBDA_TGT_ZIP} | cut -f1) -gt 50 ]; then \
                    echo $(du -ms ./dist/${LAMBDA_TGT_ZIP} | cut -f1) exceeds 50MB; \
                    false; \
                fi
                '''
                )
            }
        }
        stage("Deploy Lambda to AWS Cloud using Terraform ???") {
            steps {
                sh 'echo $PWD'
                sh 'cd ./aws_cloud && terraform init -input=false'
                sh 'echo $PWD'
                sh 'cd ./aws_cloud && terraform plan -out aws_cloud.plan -input=false'
                sh 'cd ./aws_cloud && terraform apply aws_cloud.plan'
            }
        }
    }
}
