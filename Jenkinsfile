pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        sh 'apt install rsync'
        withCredentials(bindings: [sshUserPrivateKey(credentialsId: 'HOST', keyFileVariable: 'SSH_KEY')]) {
          sh 'ssh -i ${SSH_KEY} -oStrictHostKeyChecking=no thootau@192.168.76.252 "echo test"'
        }
        withCredentials([gitUsernamePassword(credentialsId: 'thootau99',
                gitToolName: 'git-tool')]) {
         //sh 'mkdir build'
         //sh 'cp -r ./* ./build'
         sh 'rsync -av . ./build --exclude build --exclude=".*/"'
         sh 'git clone https://github.com/thootau99/ci_php_release.git'
         sh 'rm -rf ./ci_php_release/*'
         sh 'rsync -av ./build/* ./ci_php_release --exclude build --exclude=".*/"'
         //sh 'cp -r ./build/* ./ci_php_release'
         sh 'cd ci_php_release'
         sh 'version=$(cat ../.version)'
         sh 'git add . && git commit -m "PUSH TO VERSION $version"'
         sh 'git checkout -b release/version_${version} && git push -u'
         //sh 'git checkout -b release/latest && git push -u'
       } 

        sh 'cd ..'
        sh 'ls -lat'
      }
    }

  }
}
