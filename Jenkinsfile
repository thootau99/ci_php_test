pipeline {
  agent any
  stages {
    stage('Deploy') {
      steps {
        withCredentials(bindings: [sshUserPrivateKey(credentialsId: 'HOST', keyFileVariable: 'SSH_KEY')]) {
          sh 'ssh -i ${SSH_KEY} -oStrictHostKeyChecking=no thootau@192.168.76.252 "echo test"'
        }

        git(branch: 'master', credentialsId: 'COMP_SSH', url: 'https://github.com/thootau99/ci_php_release.git', poll: true)
        sh 'cd ~'
        sh 'ls -lat'
      }
    }

  }
}