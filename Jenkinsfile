node{
  stage('SCM Checkout'){
      git 'https://github.com/Freeda-F/hello-world-spring-boot'
  }

  stage('Build Docker image'){
      sh 'docker build -t freedafrancis/java-springboot-app:v2 .'
      sh 'chmod 755 container-check.sh'
   }

  stage('Push Docker image to DockerHub'){
    withCredentials([string(credentialsId: 'dockerpwd', variable: 'dockerpwd')]) {
      sh "docker login -u freedafrancis -p ${dockerpwd}"
    }
    sh 'docker push freedafrancis/java-springboot-app:v2'
   }

  stage('Copying the container-check script to remote server'){
    sshagent(['innopemFile']) {
      sh 'scp -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null /var/lib/jenkins/workspace/docker-app/container-check.sh ec2-user@172.31.8.213:/home/ec2-user/container-check.sh'
      }
   }

  stage('Deploy container on Development server'){
    def dockerRun = './container-check.sh'
    sshagent(['innopemFile']) {
    sh "ssh -o StrictHostKeyChecking=no ec2-user@172.31.8.213 ${dockerRun}"
      }
   }

}