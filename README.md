# mcd2-learning
# 前提
1. Windows11
2. visual studio code & anypoint code builder
   1. [Anypoint Code Builder の使用開始](https://docs.mulesoft.com/jp/anypoint-code-builder/start-acb)
# 動かし方
## git cloneする
1. `git clone https://github.com/nekoneko02/mcd2-learning.git`
2. 自分のGithubリポジトリへpushする
   1. Githubへログインしてリポジトリを作成する
   2. `git remote set-url origin <作成したリポジトリのURL>`
   3. `git push origin main`
   4. 作成したリポジトリへ行き、pushされていることを確認する
   5. [参考](https://qiita.com/SR_midori/items/52730907c1cddeb78b4d)
3. 指定がない限り、cloneしたディレクトリでvscodeを開く

## exchangeにpublishする
1. MuleSoftのアカウントを作成する
   1. [こちら](https://videos.mulesoft.com/friends-of-max)の右上から無料アカウントを発行できる
2. vscodeの `File > open Folder` で[mcd2-learning\mcd2-api](mcd2-learning\mcd2-api)を開く
3. vscodeのコマンドレット(ctrl+shift+p)を開く
4. `MuleSoft: Publish API Specification to Exchange` を選択する
5. 指示に従ってブラウザからログインする（ログインしていない場合）
6. business groupやversionを選択する
   1. 基本的にデフォルトのままでよい
   2. ただし、API Specificationを実装するか聞かれるが、Noを選択すればよい
      1. Yesを選択した場合は、自動でAPIkitを含むプロジェクトが選択される
7. MuleSoftにログインしてExchangeへ遷移する
   1. mcd2-apiが表示される
   2. 以下のような画面になれば成功
   3. ![alt text](images/mcd2-exchange.png)

## CICDを構築する
[GitHub 及び GitHub Actions を使った自動デプロイメント](https://jp.mulesoft-labs.dev/codelabs/auto-deployment-with-github-and-github-actions)を参考に作成している
1. 以下の手順でConnected Appを作成する
   1. 基本的に[GitHub 及び GitHub Actions を使った自動デプロイメント④](https://jp.mulesoft-labs.dev/codelabs/auto-deployment-with-github-and-github-actions/#3)の手順の通り
   2. Anypoint Platform > Access Management > Connected Appsへ遷移
   3. Nameを任意の値で入力する(ex. mcd2-cicd-app)
   4. Create Appを押下する
   5. App acts on its own behalfを選択する
   6. saveを押下する
   7. 以下の手順に従って、Connected Appに権限を追加する
      1. 作成したConnected Appを押下して設定画面に遷移する
      2. 画面下部Add Scopeを押下する
      3. 以下のScopeを選択する（Select allを押せばよい）
         1. API Manager
         2. Exchange
         3. Runtime Manager
      4. Nextを押下する
      5. 自分のBusiness Groupを選択して、Nextを押下する
      6. 自分のEnvironmentを選択して、Nextを押下する（無料アカウントの場合はSandbox）
      7. Add Scopesを押下する
      8. 画面下部のSave changesを押下する
   8. 遷移先の画面（Connected Appの一覧画面）で作成したConnected Appの右にCopy IdとCopy Secretがあるのでコピーしておく
2. 以下の手順で、Github Secretを保存する
   1. 基本的に[GitHub 及び GitHub Actions を使った自動デプロイメント⑤](https://jp.mulesoft-labs.dev/codelabs/auto-deployment-with-github-and-github-actions/#4)の手順の通り
   2. 自身のGithubのリポジトリへ行く
   3. Settings > 左ペインの Security > Secrets and variables > Actions へ遷移する
   4. New repository secretsから以下を登録する
      1. client id
         1. Name: `MULE_CONNECTED_APP_SAND_ID`
         2. Secret: `Connected App作成後にコピーしたCopy Idの値`
      2. client secret
         1. Name: `MULE_CONNECTED_APP_SAND_SECRET`
         2. Secret: `Connected App作成後にコピーしたCopy Secretの値`
3. pom.xmlにGroup Idを記載する
   1. Anypoint Platformで自分自身のGroup Idを確認する
      1. トップページのURLが `https://anypoint.mulesoft.com/home/organizations/{Group ID}/` の形式になっている
   2. [mcd2-api-implementation\pom.xml](mcd2-api-implementation\pom.xml) を開く
   3. pom.xmlの以下の部分を自分自身のGroup Idに置き換える
      1. `<mule.groupId>54054fd8-4639-4db2-a462-87ce1d04422c</mule.groupId>`
4. 以下の手順で、Github Actionをトリガする
   1. `git checkout -b dev main`
   2. `git push origin dev`
5. 以下の場所からアプリケーションがデプロイされているか確認する
   1. GithubのリポジトリのActionsタブ
      1. Greenになっていれば成功
   2. Anypoint Platform > Runtime Manager
      1. mcd2-api-implementationがデプロイされていれば成功
   3. postmanからリクエストを投げる
      1. レスポンスが返ってくれば成功
## ローカルでmavenを実行する方法
1. maven, javaをインストールする
2. settings.xmlを設定する
   1. ホームディレクトリのsettings.xmlを開く(ex. `~/.m/setting.xml`)
   2. 以下の通りにsettings.xmlを書き換える。ただし${}部分はConnected AppのId/Secretに置き換える。
```
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 https://maven.apache.org/xsd/settings-1.0.0.xsd">
  <localRepository/>
  <interactiveMode/>
  <offline/>
  <pluginGroups/>
  <servers>
    <server>
        <id>anypoint-exchange-v3</id>
        <username>~~~Client~~~</username>
        <password>${Connected App Client Id}~?~${Connected App Client Secret}</password>
    </server>
  </servers>
  <mirrors/>
  <proxies/>
  <profiles/>
  <activeProfiles/>
</settings>
```
3. `mvn package` を実行する
4. [mcd2-api-implementation\target](mcd2-api-implementation\target)にjarファイルができていれば成功