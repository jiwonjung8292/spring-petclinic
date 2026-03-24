pipeline {
    agent any
    
    tools {
        jdk 'JDK21'
        maven 'M3'
    }
    environment {
        // 환경변수 지정
        DOCKER_IMAGE_NAME = "spring-petclinic"
        // Credentials
        DOCKERHUB_CRED = credentials('dockerCredentials')
        DOCKER_API_VERSION = '1.43'
        DOCKER_BUILDKIT = '0'
    }
    stages {
        stage('Git Clone') {
            steps {
                git url: 'https://github.com/jiwonjung8292/spring-petclinic.git', 
                branch : 'main'
            }
        }
        stage('Maven Build') {
            steps {
                echo 'Maven Build'
                sh 'mvn clean package -Dmaven.test.failure.ignore=true'
            }
        }
        stage('Docker Image Create') {
            steps {
                echo 'Docker Image Create'
                sh '''
                    docker build -t ${DOCKER_IMAGE_NAME}:${BUILD_NUMBER} .
                    docker tag ${DOCKER_IMAGE_NAME}:${BUILD_NUMBER} wonji1227/${DOCKER_IMAGE_NAME}:latest
                '''
            }
        }
        stage('Docker Hub Login') {
            steps {
                echo 'Docker Hub Login'
                sh 'echo ${DOCKERHUB_CRED_PSW} | docker login -u ${DOCKERHUB_CRED_USR} --password-stdin'
            }
        }
        stage('Docker Image Push') {
            steps {
                echo 'Docker Image Push'
                sh '''
                docker push wonji1227/${DOCKER_IMAGE_NAME}:latest
                '''
            }
            post {
                always {
                    sh '''
                    docker rmi -f ${DOCKER_IMAGE_NAME}:${BUILD_NUMBER}
                    docker rmi -f wonji1227/${DOCKER_IMAGE_NAME}:latest
                    '''
                }
            }    
        }
        stage('Docker Container Run') {
            steps {
                echo 'Docker Container Run'
            }
        }

    }
}
