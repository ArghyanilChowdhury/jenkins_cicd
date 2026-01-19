pipeline {
  agent any

  environment {
    DOCKERHUB_USER = "arghyanil"      // your Docker Hub username
    IMAGE_NAME     = "jenkins_cicd"           // your Docker Hub repo name
    CREDS_ID       = "dockerhub-credentials"

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
        bat """
          docker build -t %IMG_BUILD% .
          docker tag %IMG_BUILD% %IMG_LATEST%
        """
      }
    }

    stage('Login & Push') {
  steps {
    withCredentials([usernamePassword(credentialsId: "${CREDS_ID}", usernameVariable: 'U', passwordVariable: 'P')]) {
      bat """
        docker login -u %U% -p %P%
        docker push %IMG_BUILD%
        docker push %IMG_LATEST%
      """
    }
  }
}

    stage('Cleanup ONLY This Build Image') {
      steps {
        bat """
          echo Deleting only these images:
          echo %IMG_BUILD%
          echo %IMG_LATEST%

          docker rmi -f %IMG_BUILD%
          docker rmi -f %IMG_LATEST%
        """
      }
    }
  }

  post {
    always {
      bat "docker logout"
    }
  }
}
