pipeline {
  agent any
  stages {
    stage('Deploy') {
      steps {
        withCredentials([sshUserPrivateKey(credentialsId: 'HOST', keyFileVariable: 'SSH_KEY')]) {
          sh 'ssh -i ${SSH_KEY} -oStrictHostKeyChecking=no thootau@192.168.76.252 "echo test"'
        }
	git branch: 'master',
                credentialsId: 'HOST',
                url: 'ssh://git@github.com:thootau99/ci_php_release.git'

        sh "ls -lat"
      }
    }
  }
}
