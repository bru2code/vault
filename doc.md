#!groovy 

properties([
    pipelineTriggers([pollSCM('* * * * *')]),
    [$class: 'BuildDiscarderProperty', strategy: [$class: 'LogRotator', numToKeepStr: '12']]
])

def app1 = 'nginx'
def app2 = 'python-flask'
def version = env.VERSION ?: "0.0.${env.BUILD_NUMBER}".toString()

def args = [app1: app1, app2: app2, version: version]

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
            
            sh(script: "sudo docker stop ${app1}-dev", returnStatus: true)
            sh(script: "sudo docker rm ${app1}-dev", returnStatus: true)
            sh(script: "sudo docker stop ${app2}-dev", returnStatus: true)
            sh(script: "sudo docker rm ${app2}-dev", returnStatus: true)           
           sh  """
                sudo docker run -d -p 8081:80 --name=${app1}-dev ${app1}
                sudo docker run -d -p 5001:5000 --name=${app2}-dev ${app2}
                curl -s -o /dev/null -w "%{http_code}" localhost:8081
                curl -s -o /dev/null -w "%{http_code}" localhost:5001
            """ 
        }
    }

