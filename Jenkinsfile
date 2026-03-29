pipeline {
  agent any
  options {
    disableConcurrentBuilds()
  }
  environment {
    IMAGE_NAME = "sahichoco123/multistage-flask-app"
    GIT_USER = "Saahiti-Korlam"
    GIT_EMAIL = "saahiti0699@gmail.com"
  }
  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }
    stage('Build and Push Docker Image') {
      when {
        branch 'main'
      }
      steps {
        def IMAGE_TAG = "build-${env.BUILD_NUMBER}"
        withCredentials([usernamePassword(
          credentialsId: 'dockerhub_cred',
          usernameVariable: 'DOCKERHUB_USERNAME',
          passwordVariable: 'DOCKERHUB_PASSWORD'
        )]) {
          sh ""
          "
          docker build - t $ {
            IMAGE_NAME
          }: $ {
            IMAGE_TAG
          }.
          echo "$DOCKERHUB_PASSWORD" | docker login - u "$DOCKERHUB_USERNAME"
          docker push $ {
            IMAGE_NAME
          }: $ {
            IMAGE_TAG
          }
          ""
          "
        }
        env.IMAGE_TAG = IMAGE_TAG

      }
    }
    stage('Deploy to Kubernetes') {
      when {
        branch 'main'
      }
      steps {
        withCredentials([usernamePassword(
          credentialsId: 'github_cred',
          usernameVariable: 'GITHUB_USERNAME',
          passwordVariable: 'GITHUB_TOKEN'
        )])
        sh ""
        "
        set - e
        git config--global.user.name "${GIT_USER}"
        git config--global.user.email "${GIT_EMAIL}"
        git fetch origin main
        git checkout main
        git reset--hard origin / main
        sed - i 's|image: ${IMAGE_NAME}:.*|image: ${IMAGE_NAME}:${IMAGE_TAG}|' / kubernetes / deployment.yaml
        git add / kubernetes / deployment.yaml
        git diff-- - cached--quiet || git commit - m "Update deployment with image tag ${IMAGE_TAG}"
        git push https: //${GITHUB_USERNAME}:${GITHUB_TOKEN}@github.com/Saahiti-Korlam/multi-branch-test.git main
          ""
        "
      }
    }
  }
}