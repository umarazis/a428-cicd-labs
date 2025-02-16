node {
  checkout scm
  stash includes: 'package.json', name: 'package'
  stash includes: 'jenkins/**/*', name: 'jenkins'
  stash includes: 'public/**/*', name: 'public'
  stash includes: 'src/**/*', name: 'src'
  docker.image('node:16-buster-slim').inside('-p 3000:3000') {
    stage('Build') {
      sh 'npm install'
    }
    stage('Test') {
      sh './jenkins/scripts/test.sh'
    }
    stage('Manual Approval') {
      input message: 'Lanjutkan ke tahap Deploy?'
    }
  }
}

node('ubuntu-ec2') {
  unstash 'package'
  unstash 'jenkins'
  unstash 'public'
  unstash 'src'
  docker.image('node:16-buster-slim').inside('-p 3000:3000') {
    stage('Deploy') {
      sh 'npm install'
      sh './jenkins/scripts/deliver.sh'
      sleep time: 1, unit: 'MINUTES'
      sh './jenkins/scripts/kill.sh'
    }
  }
}