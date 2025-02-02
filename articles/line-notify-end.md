---
title: "LINE Notify提供終了のお知らせが来たのでMessageing APIに移行してみた"
emoji: "🙌"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ['LINE', 'API']
published: false
---


## はじめに

LINE Notifyの提供終了が発表されました。
これに伴い、LINEのメッセージング機能を利用するためには、Messaging APIへの移行が必要となります。
本記事では、LINE NotifyからMessaging APIへの移行手順を解説します。

## LINE Notify提供終了のお知らせ

LINE Notifyの提供終了に関する詳細は、[こちら](https://notify-bot.line.me/closing-announce)から確認できます。
サービス終了に伴い、今後はMessaging APIを利用することが推奨されています。

2025年3月31日にサービスを終了となるため、現在LINE Notifyで行っている処理を止めないためには3月中に対応が完了している必要がありそうです。

なお、LINE Notifyのドキュメント類も5月12日以降に削除予定とのこと。

## Messaging APIとは

Messaging APIは、LINEの公式アカウントを通じてユーザーとコミュニケーションを取るためのAPIです。
これにより、より高度なメッセージング機能を実現することができます。

一応無料枠もありそうで、私の使用している用途としては無料分で収まりそうなので、Messaging APIに切り替えてみたいと思います。

https://developers.line.biz/ja/services/messaging-api/

## Messaging APIでできること

Messaging APIはLINE Notifyよりも高機能なので、できることが豊富にあります。

- **応答メッセージを送る**: Messaging APIを利用して、LINE公式アカウントと対話するユーザーにメッセージを返信可能。
- **任意のタイミングでメッセージを送る**: ユーザーに直接メッセージを送信可能。
- **さまざまなタイプのメッセージを送る**: テキスト、スタンプ、画像、動画、音声、位置情報、イメージマップ、テンプレート、Flex Messageなどを送信可能。
- **ユーザーが送ったコンテンツを取得する**: ユーザーが送信した画像、動画、音声、ファイルを取得可能。
- **ユーザープロフィールを取得する**: ユーザーの表示名、言語、プロフィール画像、ステータスメッセージを取得可能。
- **グループトークに参加する**: グループトークでメッセージを送信し、メンバー情報を取得可能。
- **リッチメニューを使う**: トークルーム内にリッチメニューを設定・カスタマイズ可能。
- **ビーコンを使う**: Beaconエリアに入ったユーザーとLINE公式アカウントが対話可能。
- **アカウント連携を使う**: サービスのユーザーとLINEアカウントを安全に連携可能。
- **送信メッセージ数を取得する**: LINE公式アカウントから送信したメッセージ数を取得可能。

## Messaging APIの料金

Messaging APIの料金は

毎月、一定数のメッセージを無料で送信できます。
無料メッセージ通数は、LINE公式アカウントの料金プランによって異なります。
料金プランは国によって異なる場合がありますので、該当する国の料金プランをご確認ください。


**LINE公式アカウントの料金プラン**
| プラン名               | 月額固定費<br>（税別） | 無料メッセージ通数<br>（月） | 追加メッセージ料金<br>（税別） |
|------------------------|------------------:|--------------------------:|---------------------------|
| コミュニケーションプラン | 0円               | 200通                    | 不可                      |
| ライトプラン           | 5,000円           | 5,000通                  | 不可                      |
| スタンダードプラン     | 15,000円          | 30,000通                 | ～3円/通※2                |
- ※1 メッセージ通数は、送付人数 × メッセージ通数でカウント。1通あたり3吹き出しまで送付可能。
- ※2 追加メッセージの単価は配信数によって異なります。詳細は[こちら](https://www.lycbiz.com/sites/default/files/media/jp/docs/line-official-account-price-table.pdf)からご確認ください。

:::message
スタンダードプランではない場合、無料枠を超えてメッセージを送ることは出来ません。
:::

:::message
**メッセージ通数のカウント方法について**
メッセージ通数は、メッセージの送信対象となった人数でカウントされます。
たとえばメッセージオブジェクトを4件指定したプッシュメッセージを、5人いるトークルームに対して1回送った場合、カウントされるメッセージ通数は5通です。
1回のリクエストで指定したメッセージオブジェクトの件数は、メッセージ通数には影響しません。

またLINE公式アカウントをブロックしているユーザーIDや、存在していないユーザーIDなど、メッセージが実際には届かないユーザーを宛先に指定してメッセージを送信した場合は、メッセージ通数にはカウントされません。
:::

詳細な料金プランについては、以下を参照してください。
https://developers.line.biz/ja/docs/messaging-api/overview/#line-official-account-plan
https://www.lycbiz.com/jp/service/line-official-account/plan/


## Messaging APIの始め方

ひとまずクイックスタートがあるので、クイックスタートを元にメッセージを送るまで作業を進めてみます。
[Messaging APIを始めよう](https://developers.line.biz/ja/docs/messaging-api/getting-started/)

Messaging APIの利用を開始するための手順は以下の通りです：

1. **LINE公式アカウントの作成**: LINE Developersコンソールにアクセスし、新しいプロバイダーとチャネルを作成します。
2. **Messaging APIを有効にする**: チャネルの基本設定を行い、チャネルシークレットとアクセストークンを取得します。
3. **ボット、Webhookの設定**: Webhook URLを設定し、LINEプラットフォームからのイベントを受信できるようにします。
4. **メッセージ送信の実装**: LINE Messaging APIを使用して、ユーザーにメッセージを送信するコードを実装します。例えば、Node.jsを使用してメッセージを送信する場合、以下のようなコードを使用します：


### 1. LINE公式アカウントの作成
Messaging APIを使用するためには、LINE公式アカウントを作成する必要があります。
LINE公式アカウントを作成すると、公開されてしまうのでは？という懸念がありました。
LINE公式アカウントには、認証済アカウントと、非認証アカウントの二種類のアカウントがあり、
LINEアプリの検索で表示されるのは、認証済アカウントのみとなっています。
LINE公式アカウント作成直後は非人称アカウントなので、公開されることはありません。
また、認証済アカウントにするためにはアカウント認証を手動でリクエストすることになるため、勝手に認証済アカウントになって公開される心配もないです。

https://help2.line.me/official_account_jp/android/categoryId/20008249/pc?lang=ja&contentId=20011725


#### 1-1. LINEビジネスIDに登録する
LINEアカウントまたはメールアドレスを用いて登録
https://account.line.biz/signup?redirectUri=https://entry.line.biz/form/entry/unverified


**SMS認証を行うをクリック**
![](https://storage.googleapis.com/zenn-user-upload/9ce3d7f2e99d-20250123.png)

**電話番号を入力して、SMSを送信**
![](https://storage.googleapis.com/zenn-user-upload/938d9c282bbe-20250123.png)

**スマホに届いた認証番号を入力して、確認する**
![](https://storage.googleapis.com/zenn-user-upload/92ab48ec9f5e-20250123.png)

**認証完了**
![](https://storage.googleapis.com/zenn-user-upload/61586bbfa81f-20250123.png)

#### 1-2. 作成フォームに必要事項を記入する
![](https://storage.googleapis.com/zenn-user-upload/8021e65709a9-20250123.png)

ログインしているユーザ名を確認し、問題なければ必要な項目を入力していきます。
今回は個人利用、メッセージ配信のみを想定しています。

**確認したら完了**
![](https://storage.googleapis.com/zenn-user-upload/6682b9742f63-20250123.png)

**LINE公式アカウント作成完了**
![](https://storage.googleapis.com/zenn-user-upload/428934807a57-20250123.png)

#### 1-3. LINE公式アカウントを確認する
作成されたLINE公式アカウントは、LINE Official Account Manager (opens new window)で確認できます。

LINE Official Account Managerにログインすると、同意が求められます。多分初回だけ
同意しないと先に進めないので、同意して進めます。
- 情報利用に関する同意について
- LINEヤフーグループへの情報提供に関する個別規約への同意について

作成されたアカウントが確認できます。
![](https://storage.googleapis.com/zenn-user-upload/89d1ef8fc5ad-20250123.png)

### 2. LINE公式アカウントでMessaging APIを有効にする

#### 2-1. Messaging APIの利用を有効にする
一覧のアカウントをクリックし、アカウントのページ右上にある「設定」をクリックします。
設定ページの画面左のメニューから「Messaging API」をクリックします。
表示されたページが以下
![](https://storage.googleapis.com/zenn-user-upload/610a5b247ac7-20250123.png)

「Messaging APIを利用する」をクリック

**プロバイダーを選択**
![](https://storage.googleapis.com/zenn-user-upload/ca43b8ed0a08-20250123.png)
選択が無いのは初回だからかな？適当にプロバイダー名を入れて「同意する」
プロバイダーは後から変更できないので、間違えたばあいは作り直しが必要

**プライバシーポリシーと利用規約**
![](https://storage.googleapis.com/zenn-user-upload/3b2fea379199-20250123.png)
商用の場合は必要ですが、個人利用なので入力せず「OK」

最後に確認が出るので、OKクリックでAPIの準備が整います。

![](https://storage.googleapis.com/zenn-user-upload/d1026c0ec4c9-20250123.png)


#### 2-2. LINE Developersコンソールにログインする
Messaging APIのチャネルの設定は、LINE Developersコンソールで行います。
https://developers.line.biz/ja/

ページ右上の「コンソール」からコンソールページに入れます。

#### 2-3. チャネルを確認する

コンソールが開くとプロバイダーの一覧が表示されます。
先ほどMessaging APIの有効化で入力したプロバイダーが表示されているかと思います。
**プロバイダー一覧**
![](https://storage.googleapis.com/zenn-user-upload/a27ab182a479-20250123.png)

**チャネル設定**
![](https://storage.googleapis.com/zenn-user-upload/c5f5ef81c22a-20250123.png)


チャネルを作成したところで、LINEに通知が来ると思います。
![](https://storage.googleapis.com/zenn-user-upload/ccf28b1d8f96-20250202.png)


### 3. ボット、Webhookの設定

次にボット、Webhoopkの設定となりますが、
結論としてメッセージ送るだけならボットやWebhookは不要です。
という訳で、3は飛ばします。
気が向いたら、ボットを持ちいたメッセージ送信について書くかもしれません。


ボットやWebhoookの役割として、LINEでのイベント（ユーザの公式チャンネル登録やチャンネルへのメッセージ送信など）をきっかけにメッセージを自動的に送りたい場合などには必要となります。
ここで言うボットは、いわゆるWebアプリケーションの一部と考えればよく、アクセスされたら何かしらの処理（登録者にメッセージを送る、何かのシステムに会員登録をする、システム管理者向けにメールを送る、など）が実施されるアプリケーションです。

公式LINE登録を受け付けた際に案内のメッセージを送るようなシーンを例に流れを考えると
1. ユーザがLINE公式チャンネル登録（イベントが発生）
2. イベントに応じてLINEプラットフォームがリクエストを送信。Webhookで設定したURLにリクエストが送られます。
3. LINEプラットフォームからのリクエストに応じて、ボットが処理を実施
4. ボットからメッセージを送る際、チャネルを指定してメッセージを送ります。

Messageging APIを用いてメッセージを送るためにはボットが必要です。
ボットからメッセージを送る際にチャネルを使用してメッセージを送ります。
ボットがチャネルを使用するためには、チャネルアクセストークンが必要です。
ボットにはホストサーバが必要です。
ボットにアクセスするためにWebhook URLを設定します。


### 4. メッセージ送信の実装
メッセージ送信の準備と検証を進めて行きます。

Messageging APIを用いてメッセージを送るためにはチャネルを指定します。
チャネルを通してメッセージを送るためには、チャネルアクセストークンが必要です。
そのため、まずはチャネルアクセストークンを作成します。

#### チャネルアクセストークンとは
名前の通り、チャネルにアクセスするためのトークンです。
いわゆる通行手形みたいなもので、
チャネルの利用者が、不正にアクセスしようとしていないかを確認するための仕組みです。

チャネルアクセストークンは有効期間や発行可能件数の違いにより4種類あります。

- **チャネルアクセストークンv2.1**: 任意の有効期間を指定可能（最大30日）30件まで発行可能。
- **ステートレスチャネルアクセストークン**: 15分間。無制限
- **短期のチャネルアクセストークン**: 30日間有効。30件まで発行可能。超えた場合は古いものから削除
- **長期のチャネルアクセストークン**: 有効期限なし。1件のみ。再発行可能

推奨は任意の有効期間を指定できるチャネルアクセストークンv2.1です。
jwtトークンを発行したりと、ひと手間かかるので、楽してステートレスチャネルアクセストークンを用います。

それぞれのトークンの発行方法については下の「チャネルアクセストークン」のページを参照してください。

[チャネルアクセストークン](https://developers.line.biz/ja/docs/basics/channel-access-token/)
[チャネルアクセストークンv2.1を発行する](https://developers.line.biz/ja/docs/messaging-api/generate-json-web-token/)

#### チャネルアクセストークンを準備
Messaging APIの使い方については以下のリファレンスにすべて載っています。
[Messaging APIリファレンス](https://developers.line.biz/ja/reference/messaging-api)

Messaging APIのリファレンスからチャネルアクセストークン取得について確認してみます。
[ステートレスチャネルアクセストークンを発行する](https://developers.line.biz/ja/reference/messaging-api/#issue-stateless-channel-access-token)

**エンドポイント**
```
POST https://api.line.me/oauth2/v3/token
```

**リクエストのコマンド**
```shell
curl -v -X POST https://api.line.me/oauth2/v3/token \
-H 'Content-Type: application/x-www-form-urlencoded' \
--data-urlencode 'grant_type=client_credentials' \
--data-urlencode 'client_id={channel ID}' \
--data-urlencode 'client_secret={channel secret}'
```

チャネルID`{channel ID}`とチャネルシークレット`{channel secret}`があれば使えます。
先ほど作ったチャネルのページにそれぞれ値が確認できます。

LINE Developersのコンソールからプロバイダーを選択し、「チャネル設定」からチャネルを選択します。
「チャネル基本設定」に「チャネルID」、「チャネルシークレット 」が表示されているので、
それらをコピーして値を設定します。


実行結果は以下のように返されます。
`access_token`の部分がチャネルアクアクセストークンです。
```json
{
  "token_type": "Bearer",
  "access_token": "ey....",
  "expires_in": 900
}
```

:::message
上記のコマンドはLinuxやMacでのコマンドとなっています。
WindowsのコマンドプロンプトやPowershellの場合、以下のように実行してみてください。
**コマンドプロンプト**
```
# コマンドプロンプト
curl -v -X POST "https://api.line.me/oauth2/v3/token" ^
  -H "Content-Type: application/x-www-form-urlencoded" ^
  --data-urlencode "grant_type=client_credentials" ^
  --data-urlencode "client_id={channel ID}" ^
  --data-urlencode "client_secret={channel secret}"

# コマンドプロンプト　1行で書いた版
curl -v -X POST "https://api.line.me/oauth2/v3/token" -H "Content-Type: application/x-www-form-urlencoded" --data-urlencode "grant_type=client_credentials" --data-urlencode "client_id={channel ID}" --data-urlencode "client_secret={channel secret}"
```

**Powershell**
```powershell
Invoke-RestMethod -Uri "https://api.line.me/oauth2/v3/token" `
  -Method POST `
  -Headers @{ "Content-Type" = "application/x-www-form-urlencoded" } `
  -Body "grant_type=client_credentials&client_id={channel ID}&client_secret={channel secret}"
```
:::

チャネルアクセストークンが取得できたので、メッセージを送るAPIを使ってみます。

#### ブロードキャストメッセージ送信
ブロードキャストメッセージは、公式チャンネル登録済みのユーザ全員に送るメッセージです。
そのため、チャネルアクセストークンを指定するだけでメッセージが送れます。
何かしらの告知などは、こちらを使用することになるかと思います。

[ブロードキャストメッセージを送る](https://developers.line.biz/ja/reference/messaging-api/#send-broadcast-message)

**エンドポイント**
```
POST https://api.line.me/v2/bot/message/broadcast
```

**リクエストのコマンド**
```shell
curl -v -X POST https://api.line.me/v2/bot/message/broadcast \
-H 'Content-Type: application/json' \
-H 'Authorization: Bearer {channel access token}' \
-H 'X-Line-Retry-Key: {UUID}' \
-d '{
    "messages":[
        {
            "type":"text",
            "text":"Hello, world1"
        },
        {
            "type":"text",
            "text":"Hello, world2"
        }
    ]
}'
```

結論から言うと、チャネルアクセストークン`{channel access token}`だけあればメッセージは送れそうです。
X-Line-Retry-Keyについては、リトライに使用する任意の文字列なので、設定しなくてもよさそう。
設定しない場合、`-H 'X-Line-Retry-Key: {UUID}'`の行ごと削って実行してください。

正しく実行されると、公式チャンネルに以下のようにメッセージが届きます。
![](https://storage.googleapis.com/zenn-user-upload/14ac865885fd-20250202.png)


#### プッシュメッセージを送る
ユーザー、グループトーク、または複数人トークに、任意のタイミングでメッセージを送信するAPIです。

**エンドポイント**
```
POST https://api.line.me/v2/bot/message/push
```

**リクエストのコマンド**
```shell
curl -v -X POST https://api.line.me/v2/bot/message/push \
-H 'Content-Type: application/json' \
-H 'Authorization: Bearer {channel access token}' \
-H 'X-Line-Retry-Key: {UUID}' \
-d '{
    "to": "U4af4980629...",
    "messages":[
        {
            "type":"text",
            "text":"Hello, world1"
        },
        {
            "type":"text",
            "text":"Hello, world2"
        }
    ]
}'
```

ブロードキャストとの違いは、`to`を指定する必要がありますが、基本的には同じです。


### まとめ

LINE Notifyの提供終了に伴い、Messaging APIや他のサービスへの移行は避けられません。
早めに移行を進めることで、スムーズにLINEのメッセージング機能を利用し続けることができます。
ぜひ参考サイトを活用し、移行を進めてください。

### 参考情報
[LINE Notify提供終了のお知らせ](https://notify-bot.line.me/closing-announce)
[LINE Notify API Document](https://notify-bot.line.me/doc/ja/)
[Messaging APIの料金](https://developers.line.biz/ja/docs/messaging-api/overview/#line-official-account-plan)
[LINE公式アカウントの料金プラン](https://www.lycbiz.com/jp/service/line-official-account/plan/)
[Messaging APIドキュメント](https://developers.line.biz/ja/docs/messaging-api/getting-started/)
