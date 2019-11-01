# RSocket

先日行った[SPRING FEST 2018](http://springfest2018.springframework.jp/)のKEYNOTEで紹介されていたのがきっかけで気になったので調べる

![logo](https://avatars2.githubusercontent.com/u/25459855?s=400&v=4)

http://rsocket.io/

## 概要

**Application protocol providing Reactive Streams semantics**

TCP、WebSockets、Aeronを転送プロトコルとして使用するバイナリ通信プロトコル

モバイル通信の為に通信の持続的接続とその再接続処理をプロトコルレベルでサポートしている。

- 単純なリクエストとレスポンスが対になる形式
- リクエストに対してストリームデータをレスポンスし続ける形式
- リクエストのみ送り続ける形式
- 双方向に(多重な)やりとりをする形式

これらの形式を単一の接続で処理できるように設計されている。

[Reactive Manifesto](https://www.reactivemanifesto.org/)の原則を取り入れた通信プロトコルと謳っている。(This "RSocket" protocol is a formal communication protocol that embraces the "reactive" principles.)

- 関連ドキュメント
  - [FAQ](http://rsocket.io/docs/FAQ)
  - [Motivations](http://rsocket.io/docs/Motivations)
  - [Protocol](http://rsocket.io/docs/Protocol)
  - [デモっぽいやつ](http://rsocket-demo.herokuapp.com/)

---

### 所感

転送プロトコル自体は既存の技術を使い、その上位層でのフロー制御のプロトコルとして作られたものと理解した。
多言語に対応していて、現在はJava,JavaScript,C++,Kotlinのライブラリが提供されている。(正直Reactive界隈の新技術の登場はついていけてない :cry:

現状はHTTP/1でのRESTfulAPIによるサーバ間通信がデファクトスタンダードで(弊社に限らず)、
- 接続の多重化もできず(リクエストする≒接続する)
- 同期送受信が原則で
- 双方向性もなく
- バイナリではない

と古い技術であることは明らかだが使われ続けている。  


大きな要因としては、(要は便利で簡単だから、だが)

- 'Reactive' や 'Message Driven' という概念がバックエンドエンジニアに浸透していないこと(イベント駆動や非同期処理はNodeJSが登場してか大分広まったか)
- 同期的な処理が必要な要件も多いこと
- RESTとマイクロサービスを使ったアーキテクチャパターンがいくつもあり、RESTの欠点のいくつかは対処できること(Pipeline/ApiGatewayやAsynchronous Messagingなど)
- エコシステムがまだまだ不足していること(HTTP/1自体がそもそも人間が認識できる形式がベースでその上でデバッグツールが長い歴史の中で生み出されきたというのが一番でかいのでは)

といった点があると考えている。

---

## なぜRSocketを作ったのか？

主に以下のことを満たす為に作られた

- ストリーミングがプッシュなどの従来の単一型のリクエスト/レスポンスでないインタラクションモデル
- ネットワークを超えたアプリケーションレベルのフロー制御セマンティクス
- 単一接続の多重化
- 長時間の接続の為のセッション再開のサポート
- WebSocketやAeronの為のアプリケーションレイヤのプロトコルの提供

---

### 所感

ここで気になったのはgRPCとの違い。HTTP/2を使った通信を行うということ以外は似た思想で作られているように見える。

---

## vs gRPC

https://github.com/rsocket/rsocket-rpc-java

RSocket上でのRPCライブラリ

- RSocketを通信プロトコルとして使用したRPC実装
- gRPCと同様にProdocol Bufferでのスタブ生成を行うなどgRPCと同等機能を目指している
- gRPC(HTTP/2)にはバックプレッシャーのための機能がないため高負荷時の劣化・復元力低下を招く恐れがあるため、その対策として開発されている

## 触ってみる

今回はなし！

## 他参考資料

[RSocket Java（0.11）でEcho Server／Clientっぽいものを書いてみる - CLOVER🍀](https://kazuhira-r.hatenablog.com/entry/2018/11/14/225017)

[Give REST a Rest with RSocket](https://www.infoq.com/articles/give-rest-a-rest-rsocket)

[Spring 5に備えるリアクティブプログラミング入門](https://www.slideshare.net/TakuyaIwatsuka/spring-5)

## 全体通して

実際これを取り入れようとしてもハードルは現時点では高く、ベースとなる設計思想のあり方から具体的なベストプラクティスまでわからないことも多いので、多大な学習コストを支払うか、初期コストとしてサーバリソースを必要以上に投入し非効率だが投資で補うか、ということになると消極的に後者を選ぶケースは今後も多いと思う。(そもそもReactiveの概念難しくない？)

個人的には段階の一つとして、通信のコスト削減高速化の為のソリューションとして、gRPCやRSocket-RPCを取り入れていくというのが今あるイメージ。

## おまけ(Reactiveにおける用語の捉え方)

[用語集 - リアクティブ宣言](https://www.reactivemanifesto.org/ja/glossary)

|言葉|意味|
|:--|:--|
|非同期|クライアントがリクエストをサーバに送信した後、サーバは処理を任意のタイミングで行う事。クライアントはリクエスト後に処理完了を待つ事はない状態を指す|
|バック・プレッシャー|耐障害性の為のもので、あるコンポーネントに過負荷が発生した時にクラッシュ(障害)やメッセージの喪失(復元不可)といった状況を防ぐために、送信者側のコンポーネントに状態をFBし送信量を抑える|
|弾力性(elasticity))|負荷状況の変化に応じてスケーラブルなシステムのリソースを自動的に追加または減少させること(オートスケール)|
|メッセージ駆動（イベント駆動と対比して）|イベント駆動とは区別された考え方だが正直違いがあまりわからない。とりあえずこれを読むのが良さそう。[皿洗いの7つの方法とリアクティブシステムのメッセージ駆動 - Qiita](https://qiita.com/yugolf/items/9eafa2562bc76264b406)<br>雑な解釈では、操作や処理の入出力データをメッセージとして、それを特定のコンポーネントが受信すると処理を始める。(イベント情報はメッセージの中に任意で内包する)|
|ノンブロッキング|一つのリソースを巡って競争する複数のスレッドが、それらのリソースを相互に排他的に保護することで無期限に実行が延期されるとき、あるアルゴリズムはノンブロッキング。リソース開放を待機している間に他の処理を実行できるAPI実装|
