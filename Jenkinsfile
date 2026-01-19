pipeline {
  agent any

  environment {
    DOCKERHUB_USER = "arghyanil"
    IMAGE_NAME     = "jenkins_cicd"          // Docker Hub repo name
    CREDS_ID       = "dockerhub-creds"

    TAG            = "${BUILD_NUMBER}"

    IMG_BUILD      = "${DOCKERHUB_USER}/${IMAGE_NAME}:${TAG}"
    IMG_LATEST     = "${DOCKERHUB_USER}/${IMAGE_NAME}:latest"
  }

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }

    stage('Build Docker Image') {
      steps {
        sh """
          docker build -t ${IMG_BUILD} .
          docker tag ${IMG_BUILD} ${IMG_LATEST}
        """
      }
    }

    stage('Login & Push') {
      steps {
        withCredentials([usernamePassword(credentialsId: "${CREDS_ID}", usernameVariable: 'U', passwordVariable: 'P')]) {
          sh """
            echo "$P" | docker login -u "$U" --password-stdin
            docker push ${IMG_BUILD}
            docker push ${IMG_LATEST}
          """
        }
      }
    }

    stage('Cleanup ONLY This Build Image') {
      steps {
        sh """
          echo "Deleting only these images:"
          echo " - ${IMG_BUILD}"
          echo " - ${IMG_LATEST}"

          docker rmi -f ${IMG_BUILD} || true
          docker rmi -f ${IMG_LATEST} || true
        """
      }
    }
  }

  post {
    always {
      sh "docker logout || true"
    }
  }
}
