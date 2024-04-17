pipeline {
  agent any

  stages {
    stage('Build') {
      steps {
        sh 'docker build -t $DOCKER_IMAGE .'
      }
    }
    stage('Deploy') {
      steps {
        withCredentials([usernamePassword(credentialsId: "${DOCKER_REGISTRY_CREDS}", passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
          sh "echo \$DOCKER_PASSWORD | docker login -u \$DOCKER_USERNAME --password-stdin docker.io"
          sh 'docker push $DOCKER_IMAGE'
        }
      }
    }
    stage('Build on instance') {
      steps {
	sh "sudo chmod 400 '/home/ec2-user/lulkeypair.pem'"
        sh "sudo ssh -o StrictHostKeyChecking=no -i /home/ec2-user/lulkeypair.pem ${TARGET_EC2_HOST} 'docker run --name lulbe1 --hostname lulbe1 --network lul-net -e LISTENING_PORT=8090 -d tienanhknock/lulbackend ; \
        docker run --name lulbe2 --hostname lulbe2 --network lul-net -e LISTENING_PORT=8090 -d tienanhknock/lulbackend ; \
        docker run --name nginx-docker --network lul-net -p 80:8090 -d -v /home/ec2-user/nginx.conf:/etc/nginx/nginx.conf nginx'"
      }
    }
  }
  post {
    always {
      sh 'docker logout'
    }
  }
}
