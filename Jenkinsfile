#!groovy 

properties([
    pipelineTriggers([pollSCM('* * * * *')]),
    [$class: 'BuildDiscarderProperty', strategy: [$class: 'LogRotator', numToKeepStr: '12']]
])

def application = 'nginx'
def approvalGates = env.APPROVAL_GATES ?: 'prod'
def buildLabel = env.BUILD_LABEL ?: application
def environments = env.ENVIRONMENTS ?: 'test,prod'
def project = env.PROJECT ?: 'vault'
def version = env.VERSION ?: "0.0.${env.BUILD_NUMBER}".toString()

def args = [app1ication: application,  approvalGates: approvalGates, project: project, version: version]

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
            sh '''
               docker build -t ${application} ${application}/
               
            '''
        }
        
        stage('Start application') {
            sh '''
                docker run -d -p 8081:80 --name=${application} ${application}
                curl -s localhost:8081 | grep -i "Thank you for using nginx" 
            '''
        }
    }

