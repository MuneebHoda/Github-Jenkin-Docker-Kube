pipeline {
    agent any
    tools{
        maven 'maven_3_8_4'
    }
    environment {
        dotenv '.env'
        KUBECONFIG = "${KUBECONFIG_PATH}"
        DOCKER_HUB_CREDENTIALS = "${DOCKER_HUB_CREDENTIALS}"
    }
    stages{
        stage('Build Maven'){
            steps{
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/MuneebHoda/Github-Jenkin-Docker-Kube']]])
                sh 'mvn clean install'
            }
        }
        stage('Build docker image'){
            steps{
                script{
                    sh 'docker build -t muneebhoda/devops-integration .'
                }
            }
        }
        stage('Push image to Hub'){
            steps{
                script{
                   withCredentials([string(credentialsId: 'env.DOCKER_HUB_CREDENTIALS', variable: 'dockerhubpwd')]) {
                   sh 'docker login -u muneebhoda -p ${dockerhubpwd}'

}
                   sh 'docker push muneebhoda/devops-integration'
                }
            }
        }
        stage('Deploy to k8s'){
            steps{
                script{
                    kubernetesDeploy (configs: 'deploymentservice.yaml',kubeconfigId: 'env.KUBECONFIG')
                }
            }
        }
    }
}