pipeline{
  agent any

  stages{
    stage('Maven Build'){
      steps{
        echo "${getLatestCommitId()}"
        sh "mvn clean package"
      }
    }

    stage('Docker Build Image'){
      steps{
        sh "docker build . -t thedevopsdude311/2021myapp ${getLatestCommitId()}"
      }
    }
    
    stage('push to docker hub'){
      steps{
        withCredentials([string(credentialsId: 'docker-hub', variable: 'dockerPwd')]) {
          sh "docker login -u thedevopsdude311 -p ${dockerPwd}"
          sh "docker push thedevopsdude311/2021myapp:${getLatestCommitId()}"
        }
        
      }
    }

    stage('dev-deploy'){
      steps{
        sshagent(['docker-dev']) {
            sh "ssh -o StrictHostKeyChecking=no ec2-user@172.31.34.30 docker rm -f mywebapp"
            sh "ssh -o StrictHostKeyChecking=no ec2-user@172.31.34.30 docker run -d -p 8080:8080 --name mywebapp thedevopsdude311/2021myapp:${getLatestCommitId()}"
        }
      }
    }
  }
}
def getLatestCommitId(){
  def commitId = sh returnStdout: true, script: 'git rev-parse --short HEAD'
  return commitId
}
