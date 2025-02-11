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

        stage('Run Ansible Playbook') {
            steps {
                sh 'ansible-playbook -i inventory playbook.yaml'
            }
        }
    }
}

