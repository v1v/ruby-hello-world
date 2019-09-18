#!/usr/bin/env groovy
pipeline {
  agent { label 'docker' }
  stages {
    stage('Release') {
      agent { label 'linux && immutable' }
      environment {
        RELEASE_SECRET = 'transit/keys/foo'
        RUBY_DOCKER_TAG = 'ruby:latest'
      }
      steps {
        script {
          checkout scm
          docker.image("${env.RUBY_DOCKER_TAG}").inside('-v /etc/passwd:/etc/passwd -v ${HOME}/.ssh:${HOME}/.ssh') {
            withEnv(["HOME=${env.WORKSPACE}}"]) {
              rubygemsLogin.withApi(secret: "${env.RELEASE_SECRET}") {
                sshagent(['f6c7695a-671e-4f4f-a331-acdce44ff9ba']) {
                  sh "bundle install"
                  sh "rake release"
                }
              }
            }
          }
        }
      }
    }
  }
}
