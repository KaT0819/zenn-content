---
title: "LINEにメッセージを送るプログラムを書いてみた"
emoji: "🌟"
type: "tech"
topics:
  - "go"
  - "line"
  - "linenotify"
published: true
published_at: "2023-08-12 16:01"
---


# はじめに
メッセージ送るシリーズでやっていこうと思いましたので、LINEやっていこうと思います。


# LINEにプログラムでメッセージを送る方法
LINEにメッセージを送る方法はいくつかあります。
## LINE Notify
シンプルな方法。通知を受け取るだけならこれでOK
https://notify-bot.line.me/ja/

## Messaging API
https://developers.line.biz/ja/services/messaging-api/
テキスト以外のメッセージ(スタンプ、画像、動画、音声など)を送りたい場合はこちら


ここではLINE Notifyでメッセージを送る方法について解説していきます。

## LINE Notifyとは
LINE Notifyは、LINE Messaging APIを使用して、外部のアプリケーションからLINEに通知を送るサービスです。開発者はLINE Notifyを利用して、さまざまな情報やイベントの通知をLINE上に簡単に送ることができます。

## LINE Notifyでメッセージを送る方法
サービスを登録する方法とアクセストークンを発行する方法があります。
アクセストークンの発行であればサービスを登録しなくても簡単に連携が可能です。


## アクセストークンの取得
マイページからトークンを発行します。
マイページを表示するために、トップページの右上にあるログインのリンクからログインします。

以前はメールアドレス、パスワードでしかログインできなかったのですが、
QRコードでのログインができるようになっていましたので、こちらの方が簡単だと思います。

ログインした後は「トークンを発行する」ボタンでトークンを発行します。
![](https://storage.googleapis.com/zenn-user-upload/6a3778672b44-20230730.png)

ここでは、「通知」というグループに通知を送るトークンを発行します。
![](https://storage.googleapis.com/zenn-user-upload/42eb5a2c0363-20230730.png =250x)

トークンを発行するとLINEの方に`LINE Notify`という名前でトークンを発行したメッセージが届くと思います。


発行されたトークンが表示されるので、コピーしておきましょう。
![](https://storage.googleapis.com/zenn-user-upload/9ec1cd85169b-20230730.png =250x)
もし、間違って閉じてしまった場合はもう表示されませんので、発行したトークンを削除して作り直しましょう。

発行されるとマイページに入力したトークン名で表示されます。
![](https://storage.googleapis.com/zenn-user-upload/d4247e307543-20230730.png =400x)

## メッセージを送るテスト
https://notify-api.line.me/api/notify
にPOST送信することでメッセージを送ることができます。


メッセージを受け取るための前準備として、先ほどアクセストークンを発行する際に選んだトークグループに`LINE Notify`を追加して置く必要があります。
![](https://storage.googleapis.com/zenn-user-upload/d40d5018ad80-20230731.png =250x)

```
curl -X  POST \
https://notify-api.line.me/api/notify \
 -H "Authorization: Bearer <トークン>" \
 -H "Content-Type: application/x-www-form-urlencoded" \
 -d message=テスト
```
`message=`に続けて送りたいメッセージを指定することでメッセージを送ることができます。
![](https://storage.googleapis.com/zenn-user-upload/ba84fe9f2cab-20230731.png =250x)

他のパラメータも試してみます。
```
curl -X  POST \
https://notify-api.line.me/api/notify \
 -H "Authorization: Bearer blac8qE9heNhEXO47yGaEiVAlcxIq0zOsfzwviLjm9T" \
 -H "Content-Type: application/x-www-form-urlencoded" \
 -d message=テスト \
 -d stickerPackageId=446 \
 -d stickerId=1988 \
 -d notificationDisabled=true
```

![](https://storage.googleapis.com/zenn-user-upload/3aa69bf4946f-20230731.png =250x)


他のパラメータの詳細は以下に載っています。
https://notify-bot.line.me/doc/ja/

送信可能なスタンプの一覧
https://developers.line.biz/ja/docs/messaging-api/sticker-list/


# LINEにメッセージを送るサンプル

Goでのメッセージ送信のサンプルコードです。
テストで実行したコマンドに加えて、画像ファイルのアップロードを追加してみました。

コードは例によってChatGPTにチャチャっと書いてもらいます。

## main.go
```go
package main

import (
	"bytes"
	"fmt"
	"io"
	"log"
	"mime/multipart"
	"net/http"
	"net/url"
	"os"

	"github.com/joho/godotenv"
)

func main() {
	// .envファイルから環境変数を読み込む
	err := godotenv.Load()
	if err != nil {
		log.Fatalf("Error loading .env file: %s", err)
	}

	accessToken := os.Getenv("LINE_ACCESS_TOKEN")

	// LINE Notify APIのエンドポイントURL
	apiURL := "https://notify-api.line.me/api/notify"

	// POSTリクエストのフォームデータを定義
	formData := url.Values{}
	formData.Set("message", "テスト")
	formData.Set("stickerPackageId", "8515")
	formData.Set("stickerId", "16581242")
	// formData.Set("notificationDisabled", "true") // 通知を送らない場合true

	// ファイルを開く
	file, err := os.Open("sample_image.jpeg")
	if err != nil {
		log.Fatalf("Error opening file: %s", err)
	}
	defer file.Close()

	// リクエストボディをバッファに読み込む
	var requestBody bytes.Buffer
	multipartWriter := multipart.NewWriter(&requestBody)

	// ファイルをフォームデータに追加
	fileField := "imageFile"
	filePart, err := multipartWriter.CreateFormFile(fileField, "sample_image.jpeg")
	if err != nil {
		log.Fatalf("Error creating form file: %s", err)
	}
	_, err = io.Copy(filePart, file)
	if err != nil {
		log.Fatalf("Error copying file content: %s", err)
	}

	// 他のフォームデータを追加
	for key, values := range formData {
		for _, value := range values {
			_ = multipartWriter.WriteField(key, value)
		}
	}

	// リクエストボディをクローズ
	multipartWriter.Close()

	// POSTリクエストを作成
	req, err := http.NewRequest("POST", apiURL, &requestBody)
	if err != nil {
		log.Fatalf("Error creating request: %s", err)
	}

	// ヘッダーを設定
	req.Header.Set("Authorization", "Bearer "+accessToken)
	req.Header.Set("Content-Type", multipartWriter.FormDataContentType())

	// HTTPクライアントを作成
	client := &http.Client{}

	// リクエストを送信してレスポンスを取得
	resp, err := client.Do(req)
	if err != nil {
		log.Fatalf("Error sending request: %s", err)
	}
	defer resp.Body.Close()

	// レスポンスを表示
	fmt.Println("Status Code:", resp.StatusCode)
	body, err := io.ReadAll(resp.Body)
	if err != nil {
		log.Fatalf("Error reading response body: %s", err)
	}
	fmt.Println("Response Body:", string(body))
}
```

## .env
LINE_ACCESS_TOKENに発行したトークンを設定します。
```
LINE_ACCESS_TOKEN=your-bot-token
```

## 実行結果
```
go run ./main.go
```

無事メッセージ、スタンプ、ファイルのアップロードができました！
![](https://storage.googleapis.com/zenn-user-upload/430f660aadc1-20230731.png =250x)

## まとめ
LINE Notifyの始め方や基本的な使い方についてまとめてみました。
やはり馴染みあるLINEなので導入は簡単にできる印象でした。
ちょっとした通知であったり、メッセージを送るという目的だけであれば、十分に目的を果たせると思います。

今回サンプルで書いたコードは以下にありますので、参考にして頂ければ幸いです。
https://github.com/KaT0819/line-notify-sample


## 参考

LINE Notifyに関する参考情報や公式ドキュメントへのリンクです。

LINE Notify
https://notify-bot.line.me/ja/

LINE Notify API Document
https://notify-bot.line.me/doc/ja/

送信可能なスタンプの一覧
https://developers.line.biz/ja/docs/messaging-api/sticker-list/
