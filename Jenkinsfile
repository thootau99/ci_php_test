pipeline {
  agent any
  stages {
    stage('Build') {
      // 1. 先安裝 rsync (複製檔案用)
      steps {
        sh 'apt install rsync -y'
        // 2. 把要用來放 build 完的 git 專案 clone 下來
        withCredentials([gitUsernamePassword(credentialsId: 'thootau99',
                gitToolName: 'git-tool')]) {
         //3. 這邊模仿 build 檔案，把檔案全部抓到一個 build 目錄下。
         sh 'rsync -av . ./build --exclude build --exclude=".*/"'
          
         //4. clone 前先把上次可能忘記清掉的資料夾清除
         sh 'rm -rf ci_php_release'
         sh 'git clone https://github.com/thootau99/ci_php_release.git'
          
         //5. 把該 git 專案內的內容清空，待會會把 build 好的資料全部放進來所以可以全刪
         sh 'rm -rf ./ci_php_release/*'
          
         //6. 把 build 好的檔案移動過來
         sh 'rsync -av ./build/* ./ci_php_release --exclude build --exclude=".*/"'
         
          
          //7. 開推，先設定 git 資訊；再創造一個 commit；再切到新tag內推上去 (tag為 v1 這樣的格式)
         sh 'git config --global user.email "thootau99@tutanota.com" && git config --global user.name "thootau"'
         sh 'cd ci_php_release && git add . && git commit -m "PUSH TO VERSION $(cat ../.version)"'
         sh 'cd ci_php_release && git tag -a "v$(cat ../.version)" -m "PUSH TO VERSION $(cat ../.version)" && git push origin v$(cat ../.version)'
       } 
      }
    }

  }
}
