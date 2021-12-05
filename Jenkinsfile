node{
  stage('Git Checkout'){
      git branch: 'main', credentialsId: 'myGit', url: 'https://github.com/cmalvia/demo'
  }

  stage('Build Docker Image'){
    sh 'docker build -t cmalvia/my-app:v1 .'
  }
  stage('Scan image with twistcli') {
        //withCredentials([usernamePassword(credentialsId: 'twistlock_creds', passwordVariable: 'TL_PASS', usernameVariable: 'TL_USER')]) {
            sh 'curl -k -u admin:P@ssw0rd --output ./twistcli https://ec2-35-74-244-44.ap-northeast-1.compute.amazonaws.com:8083/api/v1/util/twistcli'
            sh 'chmod a+x ./twistcli'
            sh "./twistcli images scan -u admin -p P@ssw0rd --address https://ec2-35-74-244-44.ap-northeast-1.compute.amazonaws.com:8083 cmalvia/my-app* --details"
  }
  stage('Upload Image to DockerHub'){
    withCredentials([string(credentialsId: 'DockerHub-pass', variable: 'GitHub')]) {
      sh "docker login -u cmalvia -p ${GitHub}"
    }
   sh 'docker push cmalvia/my-app:v1'
  }
  
  stage('Remove Old Containers'){
    sh 'docker rm -f my-app'
  }
  stage('Deploy-Dev'){
    sh 'docker run -d -p 80:3000 --name my-app cmalvia/my-app:v1'
    }
}
