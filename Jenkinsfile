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
          checkout scm
          docker.image("${env.RUBY_DOCKER_TAG}").inside('-v /etc/passwd:/etc/passwd -v ${HOME}/.ssh:${HOME}/.ssh') {
            withEnv(["HOME=${env.WORKSPACE}}"]) {
              sshagent(['f6c7695a-671e-4f4f-a331-acdce44ff9ba']) {
                sh 'env | sort'
                sh 'pwd'
                withCredentials([file(credentialsId: 'rubygems_file', variable: 'location')]) {
                  sh 'mkdir .gem && cp ${location} .gem/'
                }
                sh 'ls -ltra'
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
