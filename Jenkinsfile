pipeline {
  agent any
  stages {
    stage('Deploy') {
      steps {
        withCredentials(bindings: [sshUserPrivateKey(credentialsId: 'HOST', keyFileVariable: 'SSH_KEY')]) {
          sh 'ssh -i ${SSH_KEY} -oStrictHostKeyChecking=no thootau@192.168.76.252 "echo test"'
        }
        sh 'mkdir going_build && cd going_build'
        withCredentials([gitUsernamePassword(credentialsId: 'COMP_SSH',
                 gitToolName: 'git-tool')]) {
         sh 'git clone https://github.com/thootau99/ci_php_release.git'
       } 


        sh 'cd ..'
        sh 'ls -lat'
      }
    }

  }
}
