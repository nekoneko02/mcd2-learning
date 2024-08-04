# mcd2-learning
# 前提
1. Windows11
2. visual studio code & anypoint code builder
   1. [Anypoint Code Builder の使用開始](https://docs.mulesoft.com/jp/anypoint-code-builder/start-acb)
# 動かし方
## git cloneする
1. `https://github.com/nekoneko02/mcd2-learning.git`
2. 指定がない限り、cloneしたディレクトリでvscodeを開く
## exchangeにpublishする
1. MuleSoftのアカウントを作成する
   1. [こちら](https://videos.mulesoft.com/friends-of-max)の右上から無料アカウントを発行できる
2. vscodeのコマンドレット(ctrl+shift+p)を開く
3. `MuleSoft: Publish API Specification to Exchange` を選択する
4. 指示に従ってブラウザからログインする（ログインしていない場合）
5. business groupやversionを選択する（基本的にデフォルトのままでよい）
6. 最後にAPI Specificationを実装するか聞かれるが、Noを選択すればよい
   1. Yesを選択した場合は、自動でAPIkitを含むプロジェクトが選択される
7. MuleSoftにログインしてExchangeへ遷移する
   1. mcd2-apiが表示される
   2. 以下のような画面になれば成功
   3. ![alt text](images/mcd2-exchange.png)
