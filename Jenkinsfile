pipeline {
    agent any
    environment {
        DOCKER_HUB_USERNAME = "s8kevinaf02"
        ALPHA_APPLICATION_01_REPO = "alpha-application-01"
        ALPHA_APPLICATION_02_REPO = "alpha-application-02"
    }
    parameters {
        string(name: 'BRANCH_NAME', defaultValue: 'main', description: '')
        string(name: 'APP1_TAG', defaultValue: 'app1.1.1.0', description: '')
        string(name: 'APP2_TAG', defaultValue: 'app2.1.1.0', description: '')
        string(name: 'PORT_ON_DOCKER_HOST_01', defaultValue: '3000', description: '')
        string(name: 'PORT_ON_DOCKER_HOST_02', defaultValue: '3001', description: '')
    }
    stages {
        stage('Clone Repository') {
            steps {
                script {
                    git credentialsId: 'jenkins-agent-node',
                        url: 'https://github.com/s8kevinaf02/docker-hub.git',
                        branch: "${params.BRANCH_NAME}"
                }
            }
        }
        stage('Checking the code') {
            steps {
                script {
                    sh """
                        ls -l
                    """ 
                }
            }
        }
        stage('Building application 01') {
            steps {
                script {
                    sh """
                        docker build -t ${env.DOCKER_HUB_USERNAME}/${env.ALPHA_APPLICATION_01_REPO}:${params.APP1_TAG} .
                        docker images |grep ${params.APP1_TAG}
                    """ 
                }
            }
        }
        stage('Building application 02') {
            steps {
                script {
                    sh """
                        docker build -t "${env.DOCKER_HUB_USERNAME}"/"${env.ALPHA_APPLICATION_02_REPO}":"${params.APP2_TAG}" -f application-02.Dockerfile .
                        docker images |grep ${params.APP2_TAG}
                    """ 
                }
            }
        }
        stage('Deploying the application 01') {
            steps {
                script {
                    try {
                        sh """
                            docker rm -f app-contain-01 || true
                            docker run -itd -p ${params.PORT_ON_DOCKER_HOST_01}:80 --name app-contain-01 ${env.DOCKER_HUB_USERNAME}/${env.ALPHA_APPLICATION_01_REPO}:${params.APP1_TAG}
                            docker ps |grep app-contain-01
                        """ 
                    } catch (Exception e) {
                        sh """
                            echo "Error deploying application 01: ${e.message}"
                            docker logs app-contain-01
                            exit 1
                        """
                    }
                }
            }
        }
        stage('Deploying the application 02') {
            steps {
                script {
                    try {
                        sh """
                            docker rm -f app-contain-02 || true
                            docker run -itd -p ${params.PORT_ON_DOCKER_HOST_02}:80 --name app-contain-02 ${env.DOCKER_HUB_USERNAME}/${env.ALPHA_APPLICATION_02_REPO}:${params.APP2_TAG}
                            docker ps |grep app-contain-02
                        """ 
                    } catch (Exception e) {
                        sh """
                            echo "Error deploying application 02: ${e.message}"
                            docker logs app-contain-02
                            exit 1
                        """
                    }
                }
            }
        }
    }
}
