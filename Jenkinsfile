version: '3.3'

pipeline {
  agent any
  environment {
    DOCKERHUB_USER = "kurabedocker"
    BUILD_HOST = "root@192.168.190.130"
    PROD_HOST = "root@192.168.190.131"
    BUILD_TIMESTAMP = sh(script: "date +%Y%m%d-%H%M%S", returnStdout: true).trim()
  }
  stages {
    stage('Pre Check') {
      steps {
        sh "test -f ~/.docker/config.json"
        sh "cat ~/.docker/config.json | grep docker.io"
      }
    }
    stage('Build') {
      steps {
        sh "cat docker-compose.build.yml"
        sh "docker compose -f ssh://${BUILD_HOST} -f docker-compose.build.yml down"
        sh "docker -f ssh://${BUILD_HOST} volume prune -f"
        sh "docker compose -f ssh://${BUILD_HOST} -f docker-compose.build.yml build"
        sh "docker compose -f ssh://${BUILD_HOST} -f docker-compose.build.yml up -d"
        sh "docker compose -f ssh://${BUILD_HOST} -f docker-compose.build.yml ps"
      }
    }
    stage('Test') {
      steps {
        sh "docker -f ssh://${BUILD_HOST} container exec dockerkvs_apptest pytest -v test_app.py"
        sh "docker -f ssh://${BUILD_HOST} container exec dockerkvs_webtest pytest -v test_static.py"
        sh "docker -f ssh://${BUILD_HOST} container exec dockerkvs_webtest pytest -v test_selenium.py"
        sh "docker compose -f ssh://${BUILD_HOST} -f docker-compose.build.yml down"
      }
    }
    stage('Register') {
      steps {
        sh "docker -f ssh://${BUILD_HOST} tag dockerkvs_web ${DOCKERHUB_USER}/dockerkvs_web:${BUILD_TIMESTAMP}"
        sh "docker -f ssh://${BUILD_HOST} tag dockerkvs_app ${DOCKERHUB_USER}/dockerkvs_app:${BUILD_TIMESTAMP}"
        sh "docker -f ssh://${BUILD_HOST} push ${DOCKERHUB_USER}/dockerkvs_web:${BUILD_TIMESTAMP}"
        sh "docker -f ssh://${BUILD_HOST} push ${DOCKERHUB_USER}/dockerkvs_app:${BUILD_TIMESTAMP}"
      }
    }
    stage('Deploy') {
      steps {
        sh "cat docker-compose.prod.yml"
        sh "echo 'DOCKERHUB_USER=${DOCKERHUB_USER}' > .env"
        sh "echo 'BUILD_TIMESTAMP=${BUILD_TIMESTAMP}' >> .env"
        sh "cat .env"
        sh "docker compose -f ssh://${PROD_HOST} -f docker-compose.prod.yml up -d"
        sh "docker compose -f ssh://${PROD_HOST} -f docker-compose.prod.yml ps"
      }
    }
  }
}