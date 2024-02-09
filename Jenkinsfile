pipeline {
  agent any

  environment {
    SERVICE_NAME = "cont-registery"
    ORGANIZATION_NAME = "oluwoleatiks"
    DOCKERHUB_USERNAME = "atcool"
    REGISTRY_TAG      = "${DOCKERHUB_USERNAME}/${ORGANIZATION_NAME}-${SERVICE_NAME}:${BUILD_ID}"
    REPO_TAG          = "public.ecr.aws/p5j6n6c7"
    APP_NAME          = "demoapp"
    VERSION           = "${BUILD_ID}"
  }

  stages {

    stage ('Print ENVs') {
        steps {
          sh 'printenv'
        }
      }
      
    stage ('Build Image') {
      steps {
        withDockerRegistry([credentialsId: 'docker-hub', url: ""]) {
          sh 'docker build -t ${REGISTRY_TAG} .'
          sh 'docker push ${REGISTRY_TAG}'
        }
      }
    }

    stage ('Publish to Public ECR') {
      steps {
         withEnv(["AWS_ACCESS_KEY_ID=${env.AWS_ACCESS_KEY_ID}", "AWS_SECRET_ACCESS_KEY=${env.AWS_SECRET_ACCESS_KEY}", "AWS_DEFAULT_REGION=${env.AWS_DEFAULT_REGION}"]) {
           sh 'aws ecr-public get-login-password --region us-east-1 | docker login -u AWS --password-stdin ${REPO_TAG}'
           sh 'docker build -t ${APP_NAME}:${VERSION} .'
           sh 'docker tag ${APP_NAME}:${VERSION} ${REPO_TAG}/${APP_NAME}:${VERSION}'
           sh 'docker push ${REPO_TAG}/${APP_NAME}:${VERSION}'
         }
       }
    }
    stage ('Delete Images') {
      steps {
        sh 'docker rmi -f $(docker images -qa)'
      }
    }
  }
}


