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
            withEnv(["HOME=${env.WORKSPACE}"]) {
              sshagent(['f6c7695a-671e-4f4f-a331-acdce44ff9ba']) {
                withCredentials([file(credentialsId: 'rubygems_file', variable: 'location')]) {
                  sh 'mkdir .gem && cp ${location} .gem/ && chmod 0600 .gem/*'
                }
                // https://issues.jenkins-ci.org/browse/JENKINS-33171
                sh 'git remote set-url origin git@github.com:v1v/ruby-hello-world.git'
                sh 'git config -l'
                sh 'git remote -v'
                sh 'git checkout $BRANCH_NAME'
                sh 'git config --global user.email "VictorMartinezRubio@gmail.com"'
                sh 'git config --global user.name "Victor Martinez"'
                sh "bundle install"
                sh "rake release"
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
