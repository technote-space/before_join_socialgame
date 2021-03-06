---
id: doc2
title: 1章:サーバサイドと仲良くなろう
sidebar_label: 1章:サーバサイドと仲良くなろう
---

## サーバサイドって何をしてるの
クライアントサイド、あるいは通信を含まないゲームを作っている人からすると、サーバサイドの人は「何か凄い事をやってる人！」という印象になりがちです。
**もちろん凄い事はやっているのですが** 一体どんなことを知っておくとサーバサイドの人、あるいはサーバと話が出来るようになるでしょうか。

正確な知識を求める場合は基本情報を勉強したり、ネットワーク通信の技術書を読むのが良いですが、この章は最低限、クライアント側が覚えておくと嬉しい事に絞って書いていきます。

## HTTP通信って何
**一言で言うとどんなもの？**

サーバに対して何らかの情報を送って、サーバから何らかの情報を受け取る、という時の標準プロトコルです。

例えばWebブラウザは「僕はGoogle Chrome です。Yahooから検索してこのサイトに飛んできました」という情報を Github.com に送って、Githubのサーバはあなたにサイトの中身のhtmlを返しています。

クライアントのC# + Unityに対してサーバサイドはPHPやGoやRubyやPerlなど、様々な別の言語で書かれています。これら別の言語で書かれたサーバと通信できるように決めた約束事の一つがHTTP通信(HTTP1.0)だと思っておくと良いです。（注:正確には違います）

この規格があるおかげで、サーバとクライアントは別の言語で書いても大丈夫です。

**これの理解や実装が不完全だと何が起きるの？**

サーバエンジニアと話が通じなくなります。

**詳しく知りたい人は、この辺の単語でググってほしい**

- HTTP1.0
- UnityWebRequest

### ヘッダ、ボディ、request、response
HTTP通信は様々な要素があって大変ですが、Unityでサーバと通信するときは **GETとPOSTの2種を覚えておけばほとんどの場合足ります。** (PUTとDELETEはUnityバージョンによって実装が怪しかったことがあった気がします)  

会話例「このAPIってGETになってるけど、クライアントから現在の所持コイン数を渡してあげたいからPOSTにした方が良くないですか？」
### 通信エラーのリトライ処理をしよう
### Jsonって何
### 通信が永続的か永続的じゃないか
いきなり永続的、と言われてもびっくりしてしまいますよね。
ネットワーク通信は、便宜上永続的なものと、永続的じゃないものがあります。  
永続的、というのは「送ったデータをサーバ側で受け取ったあと、サーバ側で常に保持する必要がある通信」だと思ってください。

例えば野球で考えると、ピッチャーが何球目にカーブを投げて、バッターはバットを1.67秒後に振り始めて1.92秒時点でバットに当たって二遊間を抜けて…
のような細かい情報は、全部サーバに保存していたらログが大変なことになってしまいます。


なので「巨人対阪神は6/20日の試合で4:1で決着」みたいな試合結果だけは永続的に保存しておこう。みたいな感じです。

リズムゲームだったら「この曲を難易度HARDで遊ぶ」「結果のスコアは4780点」みたいな通信は永続的です。

リアルタイム対戦系のゲームであれば、対戦中の様々な情報は大体が非永続的なデータで、試合結果だけが永続的、となります。
非永続的なデータはUNETだったりPHOTONだったりMONOBITだったり自前のリアルタイムサーバで処理して、永続的なデータだけはHTTP1.0のPOSTやGETで通信します。

リアルタイム対戦系のゲームではない場合は、ほとんど全てのサーバとの通信は永続的になります。
リアルタイム対戦系のゲームの場合は、　**この通信処理は永続的にすべきかどうか** 、を考えなければいけません。

tips:開発チームに参加したら **「このゲームってリアルタイム通信はありますか？」** と聞いてみましょう。無いようであれば、あなたが考えるべきことは、永続的な通信だけになります！


### 非永続のリアルタイム通信
上で説明したようにHTTP1.0の通信はヘッダ情報などの様々な追加データが載っている為、FPSのキャラ移動など秒間10回以上更新されるリアルタイム情報に使うにはオーバーヘッドが大きすぎます。
なので、FPSゲームなどではリアルタイム通信を永続的(HTTP1.0)ではない普通のUDPやTCPベースで通信します。後述するPhotonなどがこの方式です。

#### Photonか自社製か
#### マッチメイキング
### 暗号化通信(SSL通信)

## 認証って何
**一言で言うとどんなもの？**

ソーシャルゲームはセーブデータという概念がなく、大体サーバ上でセーブされています。
このセーブデータがAさんのもの、という保証をするために、認証というのが必要になります。

//ここにもう少し詳しい説明
歴史的経緯として、端末固有の（アプリ再インストールでも消えない）固有の長い文字列(UUID)というハードウェアに紐づくデータを、ユーザ認証に使えた時代がありました。
しかし昨今のプライバシー保護の観点から、AppStore,GooglePlayStoreともに、UUIDを取得するアプリは審査時に却下されます。

なので、Unityで使う場合は
```cs
SystemInfo.deviceUniqueIdentifier
```
を端末識別に使っている人が多いと思います。

**これの理解や実装が不完全だと何が起きるの？**

AさんのアカウントにBさんがログインできて、ヤバい事になります。

あるいは、Aさんが昨日遊んだゲームを今日再開したら、また初めからになってしまいます。

**詳しく知りたい人は、この辺の単語でググってほしい**

- 匿名ログイン
- UUID

### 認証方式JWTとクッキー
### 匿名ログイン
### 認証を含むHTTP通信

### ソーシャルログインをしよう
ユーザがゲームを遊ぶ時の障壁を出来るだけ下げて、インストールして遊んでもらう人を増やす。というのがソーシャルゲームの大前提になります。
そのためには、ゲームの為だけに専用のメールアドレスとパスワードを登録してもらう、と言うのは相性が悪いです。

「とりあえずゲームを始めてもらう」ということであれば、上にあげた匿名ログインの仕組みによって実現できますが、そのままでは機種変更や（うっかりすると）再インストールなどの引継ぎが出来ないという問題があります。

そのため、メールアドレスとパスワードではなく、多くのユーザが既に使っているサービスのアカウントを使ってゲームにログイン/登録できるようにする、というアイデアが生まれました。
これがソーシャルログインです。

現在において、日本国内で配信するゲームでよく検討されるのは
- Twitterログイン
- LINEログイン
- facebookログイン
- SignInWithApple
- GooglePlayServices

あたりです。クライアントエンジニアは実装の詳細を知る必要はありませんが、どういった事に気を付けておくと良いかを以下に書きます。

#### mBaasを使う
#### 自社基盤を作るか使う
##### SignInWithApple
##### GooglePlayServices
##### Twitter

## アプリバージョンアップをしよう
**一言で言うとどんなもの？**

サーバに対してクライアントは自分のアプリバージョン情報とOS情報を送って、APIサーバから「このアプリは最新か」の結果を受け取ります。

最新ではない（あるいは、最低バージョン以下）だった場合、「アプリをバージョンアップしてください」とダイアログを出してストアのURLに誘導します。

ソーシャルゲームでよく遭遇するアレです。大体の開発現場では、Application.version ではなく独自にAndroidとiOSで別のバージョンを内部で持っている気がします。

**これの理解や実装が不完全だと何が起きるの？**

- ゲーム中にスリープして、一カ月後に再開したユーザが古いクライアントアプリのままAPIサーバにアクセス。APIの仕様が変わっている為謎のエラーをサーバが連発！
- リリースしたバージョンに致命的なチート可能バグを発見！Androidだけ先に審査を通ったけど強制アップデート機構が動かなかった！！死ぬ！

などがあります。

**詳しく知りたい人は、この辺の単語でググってほしい**

- 強制アップデート
- https://sassembla.github.io/Autoya/docs/en/update_app_version0.html

### 強制アップデートのチェックするタイミング
大体の場合は専用の [GET] /ios/version を使うより、**httpのresponse headerで判定** することが多い気がします。
専用APIで判定する場合、スリープ復帰の関係でうっかりすり抜けることがある為です。（前項参照）

### リソースバージョンとアプリバージョン
ソーシャルゲームの場合、先にシナリオやアイテムのデータだけを更新する。イベントのためにリソースデータだけ更新する。

という事が起きます。なので、UnityのApplication.versionではなく独自定義した
- AppVersion
- ResourceVersion(AssetBundleVersion)
- MasterDataVersion

を持っていることが多いです。

## In-App-Purchase（アプリ内課金,IAP）
**一言で言うとどんなもの？**

楽しいアプリ内課金です。基本プレイ無料のソーシャルゲームにおいて、きわめて大事なマネタイズポイントです。

大体はUnity IAPのpackageをベースに自前で拡張します。

歴史的な、というか偉大なるプラットフォームパワーによって30%の手数料を取られるのがつらいですが、しかし課金迂回はあまりにリスキーです。
あきらめてプラットフォーム推奨の課金処理に従いましょう。

**これの理解や実装が不完全だと何が起きるの？**

- 100魔法石を買おうとした人が「このアイテムは購入できません」と出て死ぬ
- 1万円分の魔法石を購入失敗したユーザがアプリの再インストールをした結果、魔法石が消滅。会計処理も死ぬしユーザは激おこ
- 「僕は500万円分の魔法石を買ったのに付与されてない、補填してください」というユーザさんが出て死ぬ

**詳しく知りたい人は、この辺の単語でググってほしい**

- レシート検証
- transaction queue

### レシート検証
### サブスクリプションの注意
