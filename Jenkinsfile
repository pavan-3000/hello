pipeline {
    agent any
    
    

    stages {
        stage('Hello') {
            steps {
                git branch: 'main',
                url: 'https://github.com/pavan-3000/hello'
            }
        }
        
        stage('docker-build') {
            steps {
                sh 'docker build -t hello .'
            }
        }

        stage('ecr repo') {
            steps {
                withCredentials([string(credentialsId: 'access_key', variable: 'access_key'), string(credentialsId: 'secret_key', variable: 'secret_key')]) {
                    sh '''
                    aws configure set aws_access_key_id $access_key
                    aws configure set aws_secret_access_key $secret_key

                    aws ecr describe-repositories --repository-names hello --region ap-south-1 || \
                    aws ecr create-repository --repository-name hello --region ap-south-1
                    '''
                }
            }
        }

        stage('ecr tag imag') {
            steps {
                withCredentials([string(credentialsId: 'access_key', variable: 'access_key'), string(credentialsId: 'secret_key', variable: 'secret_key')]) {
                    sh '''
                    aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 339713101508.dkr.ecr.ap-south-1.amazonaws.com
                    docker tag hello:latest 339713101508.dkr.ecr.ap-south-1.amazonaws.com/hello:${BUILD_NUMBER}
                    docker tag hello:latest 339713101508.dkr.ecr.ap-south-1.amazonaws.com/hello:latest
                    '''
                }
            }
        }
        stage('ecr push imag') {
            steps {
                withCredentials([string(credentialsId: 'access_key', variable: 'access_key'), string(credentialsId: 'secret_key', variable: 'secret_key')]) {
                    sh '''
                    docker push 339713101508.dkr.ecr.ap-south-1.amazonaws.com/hello:${BUILD_NUMBER}
                    docker push 339713101508.dkr.ecr.ap-south-1.amazonaws.com/hello:latest
                    '''
                }
            }
        }

        stage('log into eks') {
            steps {
                withCredentials([string(credentialsId: 'access_key', variable: 'access_key'), string(credentialsId: 'secret_key', variable: 'secret_key')]) {
                  aws eks update-kubeconfig --region ap-south-1 --name hello-cluster  
                }
            }
        }


        
    }
}
