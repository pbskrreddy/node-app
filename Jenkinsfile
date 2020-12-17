pipeline {
    agent any
    environment{
        DOCKER_TAG = getDockerTag()
    }
    stages{
        stage('Build docker image'){
            steps{
                sh "docker build -t . pbskr/nodeapp:${DOCKER_TAG}"
            }
        }
        stage('Docker push'){
            steps{
                withCredentials([string(credentialsId: 'docker-hub', variable: 'dockerHubpwd')]) {
                    sh "docker login -u pbskr -p ${dockerHubpwd}"
                    sh "docker push pbskr/nodeapp:${DOCKER_TAG}"
                }
            }
            
        }
    }
}

def getDockerTag(){
    def tag = sh script: 'git rev-parse HEAD', returnStdout: true
    return tag
}
