pipeline {
    agent any
    environment{
        DOCKER_TAG = getDockerTag()
    }
    stages{
        stage('Build docker image'){
            steps{
                sh "docker build . -t pbskr/nodeapp:${DOCKER_TAG}"
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
        stage('Deploy to k8s'){
            steps{
                sh "chmod +x changeTag.sh"
                sh "./changeTag.sh ${DOCKER_TAG}"
                sshagent(['kops-machine']) {
                    sh "scp -o StrictHostKeyChecking=no services.yml node-app-pod.yml ec2-user@34.200.217.137:/home/ec2-user/"
                    script{
                        try{
                            sh "ssh ec2-user@34.200.217.137 kubectl apply -f ."
                        }catch(error){
                            sh "ssh ec2-user@34.200.217.137 kubectl create -f ."
                        }
                    }
                }
            }
        }
    }
}

def getDockerTag(){
    def tag = sh script: 'git rev-parse HEAD', returnStdout: true
    return tag
}
