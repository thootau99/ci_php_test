pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        sh 'apt install rsync -y'
        withCredentials(bindings: [gitUsernamePassword(credentialsId: 'thootau99',
                        gitToolName: 'git-tool')]) {
          sh 'rsync -av . ./build --exclude build --exclude=".*/"'
          sh 'rm -rf ci_php_release'
          sh 'git clone https://github.com/thootau99/ci_php_release.git'
          sh 'rm -rf ./ci_php_release/*'
          sh 'rsync -av ./build/* ./ci_php_release --exclude build --exclude=".*/"'
          sh 'git config --global user.email "thootau99@tutanota.com" && git config --global user.name "thootau"'
          sh '''
            # 推到這個版本的 tag 上
            cd ci_php_release
            version=v$(cat ../.version)
            if [ $(git tag -l "$version") ]; then
              version=$version-$(date +"%s")-repeat
            fi
            git add .
            git commit -m "PUSH TO VERSION $version"

            git tag -a $version -m "PUSH TO VERSION $version"
            git push origin $version
          '''
          sh '''
            cd ci_php_release
            # 推到 latest 上
            git tag -fa latest -m "PUSH TO VERSION $version"
            git push --force origin latest
          '''
        }

      }
    }

    stage('Deploy') {
      steps {
        withCredentials(bindings: [gitUsernamePassword(credentialsId: 'thootau99',
                        gitToolName: 'git-tool')]) {
          sh 'git clone https://github.com/thootau99/ci_php_release.git'

        }
        withCredentials(bindings: [sshUserPrivateKey(credentialsId: 'HOST', keyFileVariable: 'SSH_KEY')]) {
          sh 'ssh -i ${SSH_KEY} -oStrictHostKeyChecking=no thootau@192.168.76.252 "mkdir -p /home/thootau/apache/release"'
          sh 'ssh -i ${SSH_KEY} -oStrictHostKeyChecking=no thootau@192.168.76.252 "rm -rf /home/thootau/apache/release/*"'
          sh 'scp -i ${SSH_KEY} -oStrictHostKeyChecking=no -r ./ci_php_release/* thootau@192.168.76.252:/home/thootau/apache/release'
        }

      }
    }

    stage('Clean') {
      steps {
        cleanWs(cleanWhenFailure: true, cleanWhenAborted: true, cleanWhenNotBuilt: true, cleanWhenSuccess: true, cleanWhenUnstable: true)
      }
    }

  }
}