def buildNumber = env.BUILD_NUMBER as int

def repo_url = 'git@bitbucket.xxxx/test.git'
def repo_branch = 'develop'

if (buildNumber > 1) milestone(buildNumber - 1)
milestone(buildNumber)

pipeline {
    agent {
        node {
            label 'node1'
        }
    }
     stages {
        stage('Fetch Code Develop') {
            steps {
                git url: repo_url, credentialsId: "xxxx", branch: repo_branch
            }
        }
        stage('Fetch Env Config for Develop') {
            steps {
                sh 'cp -r /home/tabs/gcp-deployment/deployment-config/xxxx* .'
            }
        }
        stage('Build Image for Develop') {
            steps {
                sh 'docker build --no-cache -t xxxx .'
                sh 'docker tag xx:d.1 asia.gcr.io/xxx:d.$BUILD_NUMBER'
                sh 'docker tag xx:d.1 asia.gcr.io/xxxx:d.latest'
            }
        }
        stage('Push Image to GCR') {
            steps {
                sh 'docker push asia.gcr.io/xxx:d.$BUILD_NUMBER'
                sh 'docker push asia.gcr.io/xxxx:d.latest'
            }
        }
        stage('Delete Docker Image from local') {
            steps {
                sh 'docker rmi -f xxx:d.1'
                sh 'docker rmi -f asia.gcr.io/xxx:d.$BUILD_NUMBER'
                sh 'docker rmi -f asia.gcr.io/xxx:d.latest'
            }
        }
        stage('Deploy on EKS for Develop Environment') {
            steps {
                
                sh 'kubectl set image deployment/xxx-dev xx-dev=asia.acr.io/xxx/xx:d.$BUILD_NUMBER'

            }
        }
    }
     post {
        success {
            mail to: 'mail@gmail.com,su@gmail.com',
            subject: "Pipeline Success: ${currentBuild.fullDisplayName}",
            body: "Hi Team, \n \n xxx DEVELOP already update, please take a while for minutes. \n More details : ${env.BUILD_URL}api/json \n \n ${currentBuild.currentResult}: Job ${env.JOB_NAME} ${env.CHANGES} \n \n Regards, \n Tech Team."
        }
        failure {
            mail to: 'mail@gmail.com,su@gmail.com',
            subject: "Pipeline Failed(Functional Automation Testing): ${currentBuild.fullDisplayName}",
            body: "Hi Team, \n \n xxxx DEVELOP failed,, please take check this pipeline. \n More details : ${env.BUILD_URL}api/json \n \n ${currentBuild.currentResult}: Job ${env.JOB_NAME} ${env.CHANGES} \n \n Regards, \n Tech Team."
        }
    }
}
