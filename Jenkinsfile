pipeline{
    agent any
      tools {
           maven 'maven'
         }
         environment {
         gitCommit = gitVersion()
         } 
     stages{
         stage('scm'){
             steps{
                 git credentialsId: 'github-cred', url: 'https://github.com/nagarajshobha/dockeransible.git'
             }
             
         }
         stage('maven build'){
             steps{
                 sh 'mvn clean package'
             }
         }
         stage('docker image'){
             steps{
                 sh "docker build . -t nagarajshobha/ansiblepipe:${gitCommit}"
                 
             }
         }
         stage('docker push '){
             steps{
                 withCredentials([string(credentialsId: 'dockerpass', variable: 'dockerpwd')]) {
                 sh "docker login -u nagarajshobha -p ${dockerpwd}"
                 }
                 
                 sh "docker push nagarajshobha/ansiblepipe:${gitCommit}"
             }
         }
         stage('docker deploy using ansible'){
             steps{
                 ansiblePlaybook credentialsId: 'sshdev', disableHostKeyChecking: true, extras: " -e  gitCommit=${gitCommit} ", installation: 'ansible', inventory: 'devserver.inv', playbook: 'docker-ansible.yaml'
             }
         }
     }
}
 def gitVersion(){
    def commitId = sh returnStdout: true, script: 'git rev-parse --short HEAD'
    return commitId
}
