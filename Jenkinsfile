pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        sh 'apt install rsync -y'
        withCredentials(bindings: [gitUsernamePassword(credentialsId: 'thootau99',
                        gitToolName: 'git-tool')]) {
          sh '''
            # 模擬 Build 的步驟，把檔案全部搬到一個叫 build 的資料夾內 (除了隱藏檔、build自己本身)
            rsync -av . ./build --exclude build --exclude=".*/"
          '''
          sh '''
            # 1. 把 release 這個專案 clone下來
            # 2. 刪除裡面既有的內容
            # 3. 再把我們 build 好的檔案放進去
            git clone https://github.com/thootau99/ci_php_release.git
            rm -rf ./ci_php_release/*
            rsync -av ./build/* ./ci_php_release --exclude build --exclude=".*/"

            # 設定 git 基本資料
            git config --global user.email "ci@thootau999.me" && git config --global user.name "ci-robot"
          '''
          sh '''
            # 推到這個版本的 tag 上
            cd ci_php_release

            # 定義版本號碼 (應該要寫在 .version 內)
            version=v$(cat ../.version)
            if [ $(git tag -l "$version") ]; then
              # 如果版本號碼已經存在 git 上，就補上timestamp，後面加上 repeat. 舉例來說：v1.1-1655875311-repeat
              version=$version-$(date +"%s")-repeat
            fi

            # 把目前的更改 commit 並 push 上 $version 這個 tag
            git add .
            git commit -m "PUSH TO VERSION $version"

            git tag -a $version -m "PUSH TO VERSION $version"
            git push origin $version
          '''
          sh '''
            cd ci_php_release
            # 推到 latest 上
            git tag -fa latest -m "PUSH TO VERSION $version"

            # 因為每次推的時候 latest 都會是既存的 tag，所以要強制蓋過去。
            git push --force origin latest
          '''
        }
        cleanWs(cleanWhenFailure: true, cleanWhenAborted: true, cleanWhenNotBuilt: true, cleanWhenSuccess: true, cleanWhenUnstable: true)

      }
    }

    stage('Deploy') {
      steps {
        withCredentials(bindings: [gitUsernamePassword(credentialsId: 'thootau99',
                        gitToolName: 'git-tool')]) {
          sh '''
            # 把剛剛 build 好，推上 git 的最新版本拉下來
            git clone https://github.com/thootau99/ci_php_release.git --branch latest
          '''

        }
        withCredentials(bindings: [sshUserPrivateKey(credentialsId: 'HOST', keyFileVariable: 'SSH_KEY')]) {
          sh '''
            # 1. 先把原本的部屬刪掉
            # 2. 再建一個空目錄(跟原本的部屬同名)
            # 3. 把從 git 上拉下來最新版本的檔案丟到剛剛建立的空目錄內

            ssh -i ${SSH_KEY} -oStrictHostKeyChecking=no thootau@192.168.76.252 "rm -rf /home/thootau/apache/release"
            ssh -i ${SSH_KEY} -oStrictHostKeyChecking=no thootau@192.168.76.252 "mkdir -p /home/thootau/apache/release"
            scp -i ${SSH_KEY} -oStrictHostKeyChecking=no -r ./ci_php_release/* thootau@192.168.76.252:/home/thootau/apache/release
          '''
        }

      }
    }

  }
  post {
    always {
              echo '任務結束時清除工作區域'
              deleteDir() /* clean up our workspace */
    }
  } 
}