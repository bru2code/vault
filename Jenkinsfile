#!groovy 

properties([
    pipelineTriggers([pollSCM('* * * * *')]),
    [$class: 'BuildDiscarderProperty', strategy: [$class: 'LogRotator', numToKeepStr: '12']]
])

def app1 = 'nginx'
def app2 = 'python'
def approvalGates = env.APPROVAL_GATES ?: 'prod'
def buildLabel = env.BUILD_LABEL ?: application
def environments = env.ENVIRONMENTS ?: 'test,prod'
def project = env.PROJECT ?: 'vault'
def version = env.VERSION ?: "0.0.${env.BUILD_NUMBER}".toString()

def args = [app1: app1, app2: app2, approvalGates: approvalGates, project: project, version: version]

node('localhost') {
    
        deleteDir()

        stage('Display envinorment') {
            sh '''
               env
            '''

        }

        stage('Checkout branch') {
           git(
               url: 'https://github.com/bru2code/vault.git',
               branch: 'master'
               )
        }

        stage('Build application images ') {
            sh """ 
               echo Building ${app1} 
               sudo docker build -t ${app1} ${app1}/ 
               sudo docker build -t ${app2} ${app2}/
               """
        }
        
        stage('Start application') {
            sh  """
                docker run -d -p 8081:80 --name=${app1}-dev ${app1}
                curl -s localhost:8081 | grep -i "Thank you for using nginx" 
                if (curl -s localhost:8081 | grep -i 
                docker run -d -p 5001:5000 --name=${app2}-dev ${app2}
                
            """ 
        }
    }

