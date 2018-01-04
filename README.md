# ETH ROPSTEN

イーサリアムで遊ぶやつ。

Raiden Networkで秒間10万リクエスト捌きつつ、スマートコントラクトで送金まで行う


 - https://testnet.etherscan.io/


## Start

環境構築。詳細はDockerfileを参照。
gethはEthereumのネットワークを操作するためのクライアントソフトウェア。


```
$ docker build -t ether_node .
$ docker run --rm -it -p 8545:8545 ether_node
```

testnet使う前にノードを開始させる必要があるので以下を実行。


```
$ geth --rpc --rpcaddr="0.0.0.0" --testnet --fast --cache=1024 console
```

 - ```--rpc``` RPC(Remote Procedure Call)の有効化。XML-RPCのRPCと多分同じ。PCからネットワークに接続された他のPC上でプログラムを呼び出して実行させるためのプロトコル。これがあると、ネットワークを通じて他のPCに処理をさせて、その結果だけ受け取れたりする。素敵。そうだあいつマイニングさせよう（）。
 - ```--rpcaddr="0.0.0.0"``` HTTP-RPC serverのlisten先。HTTP-RPC経由でethereumのAPIを叩きたいので。叩きたいんです。叩かせろ！（迫真）
 - ```--testnet``` Ethereumのマジネットワークではなく、テストネットワークでやりますよ的な。開発にイーサリアムとか買いたくないので。
 - ```--fast``` なんかわからんが高速同期。多分同時ダウンロードでブロックチェーンの同期してちょっと速くしてやりますよという善意。lightとかいうオプションも最近追加されたとかなんとか。
 - ```--cache=512``` MB単位。メモリのキャッシュ。
 - ```condole``` コンソールを起動して対話的にやろうね


__初回起動時はブロックチェーンのデータの同期がされるためすんごい時間かかる。MBP 2017 13inc Corei5/3.1GHz Mem16Gで3時間くらい。__

誰だ1時間とか言ってたやつは（）。

```eth.syncing```でなんか進捗っぽいのはsync中でも見れる。



## アカウントを作ったり、残額見たりとか

 - ```personal.newAccount("test")```
 - ``` > "0xef10acf6aec52cb8c8591caedede135635feae46"```
    - これでアカウントを発行出来る。引数はパスワードになってるから忘れるなよ！
 - ```eth.accounts```
    - 作ったアカウントを一覧で見れる
 - ```miner.start()```
    - 今日から君もマイナーだ。ちなみに最初に作成したアカウントに採掘したコインは割り当てられる。coinbaseとか呼ぶ偉い人達がいる。
    - DAG100%になると開始される。DAGとはブロックチェーンのハッシュ計算のために使われるデータファイルらしい。
    - ```eth.coinbase == eth.accounts[0]```が```true```になっている。変えたければ```miner.setEtherbase(eth.accounts[1])```こんな感じで変える
 - ```eth.blockNumber```
    - どれくらい掘れたか見れる
 - ```miner.stop()```
    - 採掘の停止。
 - ```eth.getBalance("アカウント")```
    - どれだけ掘れたか確認出来る。1000000000000000000で1コイン。```web3.fromWei(eth.getBalance(eth.accounts[0]),"ether")```これの方がわかりやすいかもしれない。


## 送金する
 - ```personal.newAccount("unimedia")```
    - アカウントをもうひとつ作成
 - ```personal.unlockAccount(eth.accounts[0], "test")```
    - アカウントは送金にロックがかかっているのでそのままでは出来ない。そのためロックを解除する
 - ```eth.sendTransaction({from: eth.accounts[0], to: eth.accounts[1], value: web3.toWei(1, "ether")})```
    - 1つ目のアカウントから2つ目のアカウントに1ETH送ります的な
    - 返り値

> INFO [01-04|04:11:40] Submitted transaction
> fullhash=0xebd1d3fb4b028ca952b5ff161c2f14a1270819ad5a348078dd87f13c801fdd1d recipient=0x1F428308B6C43bC926A148207A564C08F875983A
> "0xebd1d3fb4b028ca952b5ff161c2f14a1270819ad5a348078dd87f13c801fdd1d"

 - この時点ではまだトランザクションが未確定なため反映されない
 - ```eth.pendingTransactions```
    - 未確定のトランザクションを確認出来る。確定するためには新たなブロックが作られないといけないためとりあえず採掘する。
 - ```miner.start()```
 - ```web3.fromWei(eth.getBalance(eth.accounts[1]), "ether")```
    - 確認してみるとETHが増えているのがわかる

## Goで送金しつつブロックチェーンに任意のコードを刻む

 - branch
    - ETH/02


## RAIDEN Networkで秒間10万リクエスト捌く

 - branch
    - ETH/03

## スマートコントラクト+成果情報をUPで自動承認させる一連のアフィリエイトの流れを作る

