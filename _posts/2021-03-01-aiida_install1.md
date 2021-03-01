---
layout: post
title: AiiDAで面倒な作業はPythonにやらせる体制を作ろうの会（その１）
summary: |-
    　最初の環境構築手順のまとめ
author: Emi Minamitani
---

ものすごくピンポイントな情報で需要はあまりなさそうですが、日本語の文章が今の所ほとんどないので、備忘録代わりに残すことにしました。
まずはUbuntu 20.04.2LTS＋python3.8.5環境構築するまで。

## AiiDAとは?
自分の属している「計算機をつかって物性のシミュレーションをする」という分野だと、めんどくさい定形ルーチンがいくつもあるのと、
あとはそれらのインプット・アウトプットをわかりやすい形でセーブするのが結構大変です。
デジタルデータ扱ってる割に、自分は泥臭く手作業でcpやらmvやらをチマチマしている場合が多くて、毎日この作業で神経使うのはいやだ、
そろそろDXしたいなぁという要望を満たしてくれそうなのがAiiDAなので今回試した感じです。→
[AiiDA](www.aiida.net)

スイスのEPFLのグループが中心になって開発してます。
計算タスクの自動化とデータの整理に数年前から便利そうだと注目していたんですが、
初期は仕様変更が激しかったり、なんだかうまく動かんかったり…で断念してたんですが、
そろそろ安定してきたんじゃないかと思って試してみました。


始めたときに面食らったのが「node?なにそれ」です。
AiiDAは計算がどんなインプットでなされたのか、そのときにどんなコードを使ったのか、そのアウトプットは何かという、
一連のデータを別々のnodeとしてユニークIDを振って、どのIDがどんな型でどこにあるかをデータベースに格納するという形をとっているみたいです。
なので「どんなコンピュータがあるのか」「どんな計算が行われたのか」というのも全てnodeのID（AiiDAのドキュメント内ではpkと呼ぶみたいですね）を使って指定し、計算の再現性であるとか再利用性を高めるというコンセプトになってるようです。
とか文字で書いてもわかりにくいので実際に環境をつくって動かして見た手順を書きます。

## AiiDAのインストール作業
AiiDA自体はpython 3.Xで書かれており、実際にはpythonの仮想環境を作って動かすことになります。
ただ上記のようにデータベースを使い倒すので、そのためのpostgreSQLやRabbitMQなどの設定が必要になります。この辺りの関係か、OSのバージョンにも少し依存している感触があります。
自分の今回の設定ではUbuntu 20.04.2LTS、python3.8.5を使ってます。（MacOSのMojaveやCatalinaではまだ自分はうまく行ってない。）
AiiDA本体のバージョンは1.5.2です。

### Ubuntu側での設定＋AiiDA環境構築

```
sudo apt install git postgresql postgresql-server-dev-all postgresql-client rabbitmq-server
```

```
sudo apt install python3-dev python3-pip python3-venv
```
これで必要なデータベース周りと、python3の環境を入手。

そこで、早速AiiDAを動かすための仮想環境を入れるためのディレクトリを作って、
仮想環境を作成＆起動してみます。自分の場合は`$HOME/env` を仮想環境用のディレクトリにして`aiida-venv`に仮想環境を作りました。

```
mkdir env
cd env
python3 -m venv aiida-venv
source ./aiida-venv/bin/activate
pip install --upgrade pip
pip install aiida-core
reentry scan
```

このaiida-coreというのがAiiDAの基本部分です。
各シミュレーションソフトウェア用のプラグインも同様に`pip　install`で入れていけますが、
プラグインを入れるたびに、`reentry scan`というステップを踏まないといけないとのことです。

というわけで仮想環境にはいったところで、AiiDAを動かすためのプロファイルを作ります

```
verdi quicksetup
```
これは最低限の設定が書かれたプロファイルを作るためのコマンドで対話的に作業が進みます。氏名とかは以下では適当にhogehogeとかにしてます

```
Info: enter "?" for help
Info: enter "!" to ignore the default and set no value
Profile name [quicksetup]:
Email Address (for sharing data): hoge@hogehoge.com
First name: hoge
Last name: hoge
Institution: hoge
Trying to become 'postgres' user. You may be asked for your 'sudo' password.
Success: created new profile `quicksetup`.
Info: migrating the database.
Operations to perform:
  Apply all migrations: auth, contenttypes, db
Running migrations:
...#なんかいっぱいメッセージが出てくる
Success: database migration completed.
```
データベース作るときにsudoで使うパスワードを求められる場合があります。
でここまで進んだら、計算ジョブの管理などを行うdaemonを立ち上げます

```
verdi daemon start 2
```
これでちゃんと環境できているのか確認してみましょう

```
verdi status
```
このコマンドで

```
 ✔ config dir:  /home/hoge/.aiida
 ✔ profile:     On profile quicksetup
 ✔ repository:  /home/emi/.aiida/repository/quicksetup
 ✔ postgres:    Connected as uuidの番号@localhost:5432
 ✔ rabbitmq:    Connected as amqp://guest:guest@127.0.0.1:5672?heartbeat=600
 ✔ daemon:      Daemon is running as PID 8583 since 2021-02-11 04:33:03
```
みたいに5つの項目にチェックマークが入っていればOKです。
とりあえず第一回はこのへんで。
