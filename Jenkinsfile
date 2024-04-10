pipeline {
  agent any

  stages {
    stage('Build') {
      steps {
        sh 'docker build -t $DOCKER_TAG .'
        sh 'docker tag $DOCKER_TAG $DOCKER_IMAGE'
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
  }
  post {
    always {
      sh 'docker logout'
    }
  }
}