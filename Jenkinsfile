pipeline {
    agent any

    environment {
        GIT_CREDENTIALS_ID = 'd35ce7f2-b716-4016-bdd6-21c03564c4d9'
        DOCKER_CREDENTIALS_ID = '3ca2a147-6062-46cf-bf75-2755bf37684a'
        DOCKER_IMAGE_NAME = 'negm8/weather-app'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', credentialsId: env.GIT_CREDENTIALS_ID, url: 'https://github.com/sherifnajmeldein/weather-app'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${env.DOCKER_IMAGE_NAME} ."
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withDockerRegistry([credentialsId: env.DOCKER_CREDENTIALS_ID, url: 'https://index.docker.io/v1/']) {
                    sh "docker push ${env.DOCKER_IMAGE_NAME}"
                }
            }
        }
      
        stage('Fix Private Key Permissions') {
            steps {
                script {
                    sh "chmod 600 ./ansible/private_key_vm1"
                    sh "chmod 600 ./ansible/private_key_vm2"
                }
            }
        }
        stage('Run Ansible Playbook') {
            steps {
                sh 'ansible-playbook -i ansible/inventory ansible/deploy.yaml'
            }
        }
     }
  
    post {
        success {
            mail to: 'negmsherif8@gmail.com',
                subject: "Jenkins Build Successful: ${env.JOB_NAME}",
                body: "The build #${env.BUILD_NUMBER} has completed successfully. Check Jenkins for details."
        }
        failure {
            mail to: 'negmsherif8@gmail.com',
                subject: "Jenkins Build Failed: ${env.JOB_NAME}",
                body: "The build #${env.BUILD_NUMBER} has failed. Check Jenkins for details."
        }
    }
}
