#!/usr/bin/env groovy
pipeline {
  agent { label 'docker' }
  stages {
    stage('Release') {
      agent { label 'linux && immutable' }
      environment {
        RUBY_DOCKER_TAG = 'ruby:latest'
      }
      steps {
        script {
          gitCheckout()
          docker.image("${env.RUBY_DOCKER_TAG}").inside('-v /etc/passwd:/etc/passwd -v ${HOME}/.ssh:${HOME}/.ssh') {
            withEnv(["HOME=${env.WORKSPACE}"]) {
              sshagent(['f6c7695a-671e-4f4f-a331-acdce44ff9ba']) {
                withCredentials([file(credentialsId: 'rubygems_file', variable: 'location')]) {
                  sh 'mkdir .gem && cp ${location} .gem/ && chmod 0600 .gem/*'
                }
                sh '.ci/prepare-git-context.sh'
                sh 'rake release'
              }
            }
          }
        }
      }
      post {
        always {
          deleteDir()
        }
      }
    }
  }
}
