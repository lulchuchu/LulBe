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
        sshagent(credentials: ['ssh-cred']) {
            sh """
                ssh -o StrictHostKeyChecking=no ec2-user@ec2-3-25-103-163.ap-southeast-2.compute.amazonaws.com '
                    docker run --name lulbe1 --hostname lulbe1 --network lul-net -e LISTENING_PORT=8090 -d tienanhknock/lulbackend
                    docker run --name lulbe2 --hostname lulbe2 --network lul-net -e LISTENING_PORT=8090 -d tienanhknock/lulbackend
                    docker run --name nginx-docker --network lul-net -p 80:8090 -d -v nginx.conf:/etc/nginx/nginx.conf nginx
                '
            """
        }
      }
    }
  }
  post {
    always {
      sh 'docker logout'
    }
  }
}
