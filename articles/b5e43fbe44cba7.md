---
title: "Google Cloud Identity Platformにおけるマルチテナンシーでパスワードポリシーを更新する方法"
emoji: "✳️"
type: "tech"
topics:
  - "go"
  - "gcp"
  - "gcloud"
  - "gc24"
  - "パスワードポリシー"
published: true
published_at: "2024-02-28 12:00"
publication_name: "rescuenow"
---

## はじめに
Google Cloud Identity Platform（以降Identity Platform）は、セキュリティとアクセス管理を強化するためのパスワードポリシーを含む、柔軟なアイデンティティ管理ソリューションを提供します。
本記事では、マルチテナンシー構成におけるパスワードポリシーの設定と更新方法に焦点を当て、Go言語を用いた具体的な実装を紹介したいと思います。
マルチテナンシー構成を適切に管理することで、異なる顧客や組織間でセキュリティポリシーを効率的に適用し、アプリケーションのセキュリティレベルを向上させることが可能です。

この後の内容を見ていく上で、前提となる認証周りの構成についてまとめておきます。
**認証に関する構成**
- Identity Platformを利用してユーザ認証を実施
- Identity Platformは、マルチテナンシー構成であり、テナント単位でユーザを管理
- フロント、バックエンドはAWSのECSにて構築
- バックエンド側のAPIにて、Identity Platformによるユーザ認証を実施
- Identity Platformへのアクセスは、GoのSDKを使用
- GCPリソースの認可はサービスアカウントで制御

## 背景
Identity Platformのパスワード最小桁数はデフォルトで6桁となっています。
この桁数では昨今のセキュリティ脅威に対して不十分な保護しか提供できません。
特に、ブルートフォース攻撃や辞書攻撃といった一般的な手法により、6桁のパスワードは容易に突破される可能性があります。
この課題に対処するため、より強固なパスワードポリシーへの更新が必要とされました。

対応策の初期調査段階ではGoのSDKに該当するメソッドがあると思っていたのですが、
GoのSDKには該当するメソッドがありませんでした。
他言語も同様で、ざっと見た限りではNodejsでのみ提供されてそうな状況です。
さらに、GCPのコンソールでもパスワード桁数の更新する操作ができませんでした。
唯一REST APIでは更新機能が、提供されていることが分かりましたので、API呼び出しによる更新を実装する方針となりました。

まとめると、
1. Identity Platformのパスワード桁数は最小6桁なので、パスワード桁数を増やしてセキュリティレベルを上げたい
２. GoのSDKではパスワード最小桁数を変更できない
3. GCPコンソールにもパスワード最小桁数を更新する機能は提供されていない
4. REST APIでなら、パスワード最小桁数を更新可能
5. では、やってみよう

### GoのSDKにおけるテナント更新処理の状況
GoのSDKにはテナントの更新機能は存在していますが、更新対象が制限されています。
以下はSDKの該当箇所のソースです。

https://github.com/firebase/firebase-admin-go/blob/v4.10.0/auth/tenant_mgt.go#L145-L173

153行目の`tenant.params.UpdateMask()`にて更新用のパラメータを生成しています。
`UpdateTenant`メソッドの引数である、`tenant *TenantToUpdate`が更新する値が含まれます。

`TenantToUpdate`の定義が以下ですが、
公開されているメソッドは4つしかなく、パスワードポリシーの更新用のメソッドはありません。

https://github.com/firebase/firebase-admin-go/blob/v4.10.0/auth/tenant_mgt.go#L263-L297


## Identity Platformとは
Identity Platform は、Google が提供する顧客IDとアクセス管理 (CIAM) サービスです。
Webアプリ、モバイルアプリに認証機能を簡単に導入できます。

Identity Platformを使用すると、開発者はユーザーの認証とアクセス管理機能を簡単にアプリケーションに統合できます。特にマルチテナンシーでは、異なるテナントごとにカスタマイズされたパスワードポリシーを設定することが可能となり、企業や組織はそのセキュリティを強化できます。

### 主な機能:
- **多様な認証プロバイダ**
下記のように多くの認証方法を提供
    - メールアドレス、パスワード
    - OIDC
    - SAML
    - Google
    - Twitter
    - Facebook
    - Microsoft
    - Apple
    - LinkedIn
    - Yahoo
    - Play Games
    - GitHub
    - 電話
    - 匿名

- **SMSを利用した多要素認証**
Identity Platform は、多要素認証、ソーシャルログイン、エンタープライズサポートなどの機能を提供します。

- **パスワードポリシー**
パスワードの桁数や含めるべき文字種の設定が可能。

- **マルチテナンシー**
Identity Platformのマルチテナンシー機能は、一つのGCPプロジェクト内で複数の組織や部門を論理的に隔離し、それぞれ独立したユーザー管理を可能にする機能です。
これにより、データのセキュリティとプライバシーを確保しつつ、異なるテナントごとにカスタマイズされた認証とアクセス管理ポリシーを適用できます。マルチテナンシーは、サービスプロバイダーが一つのアプリケーションで複数の顧客をサポートする際に特に有用です。

- **マルチテナンシー関連のドキュメント**
    - [Identity Platform マルチテナンシー](https://cloud.google.com/identity-platform/docs/multi-tenancy)
    - [マルチテナンシーの設定](https://cloud.google.com/identity-platform/docs/multi-tenancy-quickstart)
    - [マルチテナントを使用した認証](https://cloud.google.com/identity-platform/docs/multi-tenancy-authentication)


以降では、実際にテナントのパスワードポリシー更新をコマンドやコード例踏まえて解説していきます。

## APIを用いたテナントのパスワードポリシー更新の手順
手順と言えるほどでもないですが、下記のとおりです。

1. アクセストークンを取得
2. テナント更新のAPIを使用してパスワードポリシーを更新
    - アクセストークンを指定して、REST APIのテナント更新

これはGoogle Cloudの他のAPIでも同様になります。

### 1. アクセストークンを取得
GCPでの認証方法は様々あり、どれを使うのが良いかわからなくなりますが、
ユースケースによって適した認証方法を選択するためのフローチャートがドキュメントとして用意されていますので、参考にするのもよいでしょう。
https://cloud.google.com/docs/authentication?hl=ja

今回の場合、アクセストークンの取得はサービスアカウントを用いて行います。

:::message
アクセストークンの取得方法について仕様調査する際、Identity Platformをキーワードに含めると、
Identity Platform上で管理しているユーザを認証するためのアクセストークンの取得方法が出てきます。
これは、Identity Platformの設定を更新するためのアクセストークンではないため、混同しないよう注意しましょう。
:::


### テナント更新のAPIを使用してパスワードポリシーを更新
以下がテナント更新APIの概要です。

| 項目               | 詳細 |
|--------------------|-------------------------------|
| **エンドポイント** | `https://identitytoolkit.googleapis.com/v2/projects/プロジェクトID/tenants/テナントID` |
| **メソッド**       | `PATCH` |
| **クエリパラメータ** | `updateMask`: string (FieldMask format) |
| **FieldMask**   | [FieldMaskの詳細](https://protobuf.dev/reference/protobuf/google.protobuf/#field-mask) |
| **リクエストボディ** | [Tenantのjson構造体](https://cloud.google.com/identity-platform/docs/reference/rest/v2/projects.tenants#Tenant) |
| **レスポンスボディ** | リクエストと同様の`Tenant`のjson構造体 |

- 認可範囲
次の OAuth スコープのいずれかが必要です。
https://www.googleapis.com/auth/identitytoolkit
https://www.googleapis.com/auth/firebase
https://www.googleapis.com/auth/cloud-platform
※APIを実行するためにはアクセストークン生成時にこれらのスコープを指定してトークンを生成する必要があります。

テナントの更新APIは更新したい値をボディに指定しますが、
それとは別に、`updateMask`というキーのパラメータを指定する必要があります。
このパラメータは、`FieldMask`という特別な形式であり、更新する値と対になっている必要があります。
簡単に言うと、更新対象を含むjsonをボディに指定し、`FieldMask`ではjson内で更新したい値を`.`で区切った階層表現で指定します。

**例１）**
ボディ
```
f {
  a : 22
  b {
    d : 1
    x : 2
  }
  y : 13
}
z: 8
```

`FieldMask`
```
updateMask=f.a
```

この場合は`f`キー内の`a`キーの値のみが更新します。

**例２） ボディは一緒で`FieldMask`の指定を変えた複数項目更新の例**
`FieldMask`
```
updateMask=f.a,f.b
```

この場合、`a`の値と、`f`キー内の`b`キー内の値を更新します。
`f.b`を指定すると、`b`キーの子要素である、`d`と`x`の値が更新されます。

**例３）今回のパスワードポリシーの変更例**
ボディ
```
{
  "passwordPolicyConfig": {
    {
      "passwordPolicyEnforcementState": "ENFORCE",
      "passwordPolicyVersions": [
        {
          {
            "customStrengthOptions": {
              {
                "minPasswordLength": integer,
                "maxPasswordLength": integer,
                "containsLowercaseCharacter": boolean,
                "containsUppercaseCharacter": boolean,
                "containsNumericCharacter": boolean,
                "containsNonAlphanumericCharacter": boolean
              }
            },
            "schemaVersion": integer
          }
        }
      ],
      "forceUpgradeOnSignin": boolean,
      "lastUpdateTime": string
    }
  },
}
```
`FieldMask`
```
updateMask=passwordPolicyConfig.passwordPolicyEnforcementState,passwordPolicyConfig.passwordPolicyVersions.customStrengthOptions
```

**パラメータの解説**
- minPasswordLength： パスワードの最小桁数
- maxPasswordLength"： パスワードの最大桁数
- containsLowercaseCharacter： パスワード内に英語小文字を含める必要あり
- containsUppercaseCharacter： パスワード内に英語大文字を含める必要あり
- containsNumericCharacter： パスワード内に数字を含める必要あり
- containsNonAlphanumericCharacter： パスワード内に英数字以外を含める必要あり
- forceUpgradeOnSignin： 既存ユーザのログイン時にパスワードポリシーに準拠しない場合にエラーになります。そのままではログイン不可となり、パスワード変更が必須となります。既存ユーザに影響与えない場合は`false`とするとよいです。

他にも指定可能なパラメータがいくつかありますので、気になる方は以下を参照してください。
https://cloud.google.com/identity-platform/docs/reference/rest/v2/projects.tenants#passwordpolicyconfig

**テナント更新API**
https://cloud.google.com/identity-platform/docs/reference/rest/v2/projects.tenants/patch

## ドキュメントページでのテスト
GCPのAPIドキュメントの素敵な点として、
ドキュメント内でAPIのテスト実行ができてしまいます。
ページ右の「試してみる」ボタンをクリックし、必要なパラメータを入力することで、APIが実行され、レスポンスも確認できます。
テナントの更新APIは、パラメータが`FieldMask`という特殊な形式だったので、
正しいパラメータの指定方法を確認できたのは良かったです。

![](https://storage.googleapis.com/zenn-user-upload/5cd15a0aa34d-20240225.png)

パラメータの補完機能もあり、高機能です。
![](https://storage.googleapis.com/zenn-user-upload/650fc1224c03-20240225.png)

テナントの更新APIページで、必要な情報を設定して、実行した結果
![](https://storage.googleapis.com/zenn-user-upload/a64c71f9c06e-20240225.png)


このテスト機能、便利なのですが、ログインしているGoogleアカウントでしか試せません。
リクエスト的にはAPIキーが発行されてそうな雰囲気です。
次に、サービスアカウントを用いたAPI呼び出しをアクセストークン発行からテナント更新までgcloudコマンドで、試してみました。

## gcloudコマンドでの動作確認
~~プログラム実装したらトークンエラーで動かなかったので~~
プログラムによる実行の前にコマンドによる動作を確認していきます。

手順は以下
0. 事前準備
    - Identy Platformでテナントを作成
    - サービスアカウントの鍵生成
    - Cloud Shellの起動
    - サービスアカウントjsonの配置
1. サービスアカウントでログイン
    - アクセストークンの確認
2. テナントの更新
    - 更新前のテナントの状態確認
    - テナントの更新
    - 更新後のテナントの状態確認

### 0. 事前準備
**Idenity Platformでテナントを作成**
Identity Platformのページを開き、テナントを作成していきます。
はじめてページを開いた場合は有効化が必要です。

- マルチテナントの設定
    - 「設定」の「セキュリティ」タブを開き、「テナントを許可」をクリック
- テナントのページを開き、テナントを追加
![](https://storage.googleapis.com/zenn-user-upload/ed156751b2e9-20240226.png)
- テナントの編集（鉛筆アイコン）を開き、「Email / Password」を有効にします。

**サービスアカウントの鍵生成**
「IAMと管理」の「サービスアカウント」を開きます。
すでにIdentity PlatformのSDK（名前はFirebase Admin SDKですが）用のサービスアカウントが作成されていますので、このサービスアカウントの鍵を作成します。
![](https://storage.googleapis.com/zenn-user-upload/506c4acfb677-20240227.png)

鍵を管理
![](https://storage.googleapis.com/zenn-user-upload/5d00e202bfd5-20240227.png)

新しい鍵を作成
![](https://storage.googleapis.com/zenn-user-upload/5618616cfbfe-20240227.png)

JSONを選択し、作成
![](https://storage.googleapis.com/zenn-user-upload/1dc43bd55c0f-20240227.png)


**Cloud Shellの起動**
実行環境はCloud Shellにします。
gcloudコマンドが使えればローカルでも大丈夫です。

コンソール右上のアイコンからCloud Shellを起動できます。
![](https://storage.googleapis.com/zenn-user-upload/ae9c94962ec1-20240225.png)


**サービスアカウントjsonの配置**
先ほど作成したサービスアカウントのcredentialのjsonファイルをCloud Shellにアップロードします。
![](https://storage.googleapis.com/zenn-user-upload/a93c30219701-20240226.png)

これで準備が整いました。

### 1. サービスアカウントてログイン
配置したサービスアカウントのファイルを指定してサービスアカウントとしてログインします。

```shell
gcloud auth activate-service-account --key-file=service-account.json

Activated service account credentials for: [firebase-adminsdk-58bkz@identity-platfrom-test-415414.iam.gserviceaccount.com]
```

- **アクセストークンの取得**
`gcloud` コマンドを使用することで、Google Cloud APIへの認証に使用するアクセストークンを取得できます。

```shell
gcloud auth print-access-token

ya29.c.c0AY_VpZir3oAqAcqITuBwCW5vaf_iOrBQgqAWY-...
```


### 2. テナントの更新
`curl` を使用して、Identity PlatformのAPIエンドポイントに対してPATCHリクエストを送信し、テナントのパスワードポリシーを更新します。

**更新前のテナントの状態**
- テナント一覧
```shell
curl -X GET -H "Authorization: Bearer $(gcloud auth print-access-token)" \
-H "Content-Type: application/json" \
'https://identitytoolkit.googleapis.com/v2/projects/{プロジェクトID}/tenants

# レスポンス
{
  "tenants": [
    {
      "name": "projects/{プロジェクトID}/tenants/tenant1-b639m",
      "displayName": "tenant1",
      "allowPasswordSignup": true,
      "inheritance": {}
    }
  ]
}
```

- テナント詳細
```shell
curl -X GET -H "Authorization: Bearer $(gcloud auth print-access-token)" \
-H "Content-Type: application/json" \
'https://identitytoolkit.googleapis.com/v2/projects/{プロジェクトID}/tenants/{テナントID}

# レスポンス
{
  "name": "projects/{プロジェクトID}/tenants/tenant1-b639m",
  "displayName": "tenant1",
  "allowPasswordSignup": true,
  "hashConfig": {},
  "inheritance": {}
}
```

**テナントの更新**
```shell
curl -X PATCH -H "Authorization: Bearer $(gcloud auth print-access-token)" \
  -H "Content-Type: application/json" 'https://identitytoolkit.googleapis.com/v2/projects/{プロジェクトID}/tenants/{テナントID}?updateMask=passwordPolicyConfig%2CpasswordPolicyConfig.passwordPolicyVersions&alt=json' \
  -d '{"passwordPolicyConfig":{"passwordPolicyVersions":[{"customStrengthOptions":{"minPasswordLength":10}}],"passwordPolicyEnforcementState":"ENFORCE","forceUpgradeOnSignin":false}}'

# レスポンス
{
  "name": "projects/{プロジェクトID}/tenants/tenant1-b639m",
  "displayName": "tenant1",
  "allowPasswordSignup": true,
  "inheritance": {},
  "passwordPolicyConfig": {
    "passwordPolicyEnforcementState": "ENFORCE",
    "passwordPolicyVersions": [
      {
        "customStrengthOptions": {
          "minPasswordLength": 10
        },
        "schemaVersion": 1
      }
    ],
    "lastUpdateTime": "2024-02-27T01:38:52.852791Z"
  }
}
```

**更新後のテナントの状態**
更新後に再度確認してみると、パスワードポリシーの定義が追加されていることがわかります。


- テナント一覧
```shell
curl -X GET -H "Authorization: Bearer $(gcloud auth print-access-token)" \
-H "Content-Type: application/json" \
'https://identitytoolkit.googleapis.com/v2/projects/{プロジェクトID}/tenants

# レスポンス
{
  "tenants": [
    {
      "name": "projects/{プロジェクトID}/tenants/tenant1-b639m",
      "displayName": "tenant1",
      "allowPasswordSignup": true,
      "inheritance": {},
      "passwordPolicyConfig": {
        "passwordPolicyEnforcementState": "ENFORCE",
        "passwordPolicyVersions": [
          {
            "customStrengthOptions": {
              "minPasswordLength": 10
            },
            "schemaVersion": 1
          }
        ],
        "lastUpdateTime": "2024-02-27T01:38:52.852791Z"
      }
    }
  ]
}
```

- テナント詳細
```shell
curl -X GET -H "Authorization: Bearer $(gcloud auth print-access-token)" \
-H "Content-Type: application/json" \
'https://identitytoolkit.googleapis.com/v2/projects/{プロジェクトID}/tenants/{テナントID}

# レスポンス
{
  "name": "projects/{プロジェクトID}/tenants/tenant1-b639m",
  "displayName": "tenant1",
  "allowPasswordSignup": true,
  "hashConfig": {},
  "inheritance": {},
  "passwordPolicyConfig": {
    "passwordPolicyEnforcementState": "ENFORCE",
    "passwordPolicyVersions": [
      {
        "customStrengthOptions": {
          "minPasswordLength": 10
        },
        "schemaVersion": 1
      }
    ],
    "lastUpdateTime": "2024-02-27T01:38:52.852791Z"
  }
}
```


## Goでの実装手順
### ステップ 1: 認証情報の取得
サービスアカウントを使用してGoogle Cloud APIに認証するためのアクセストークンを取得します。
このトークンは、APIリクエストの認証ヘッダーに含める必要があります。

```go
func (t *Tenant) getToken(ctx context.Context, tenantID string) (string, error) {
    // 環境変数にサービスアカウントのcredentialsを保持しています。ファイルから読み込むのと同等
	jsonKey := []byte(os.Getenv("FIREBASE_CREDENTIALS_JSON"))
	// サービスアカウントを使用してアクセストークンを生成
	conf, err := google.CredentialsFromJSON(ctx, jsonKey, "https://www.googleapis.com/auth/cloud-platform")
	if err != nil {
		return "", err
	}

	token, err := conf.TokenSource.Token()
	if err != nil {
		return "", err
	}

	return token.AccessToken, err
}
```

コマンド実行時と異なるポイントとしては、アクセストークン生成時に利用するAPIを限定する「scope（スコープ）」を指定する必要があります。
`conf, err := google.CredentialsFromJSON(ctx, jsonKey, "https://www.googleapis.com/auth/cloud-platform")`の`"https://www.googleapis.com/auth/cloud-platform"`部分です。
何を指定するかは呼び出し対象APIのページにある「認可範囲」で確認できます。

テナント更新APIでは下記です。
https://cloud.google.com/identity-platform/docs/reference/rest/v2/projects.tenants/patch#authorization-scopes

### ステップ 2: テナントの更新
Go言語を使用してHTTPリクエストを送信します。
特別な記述はなく、gcloudで確認したAPIの呼び出しをそのままコード化したイメージと相違ないです。

```go
// 引数として、更新対象のテナントIDと、先ほど生成したアクセストークンを受け取っています。
func (t *Tenant) updateTenant(tenantID, token string) error {
	projectID := os.Getenv("PROJECT_ID")
	// Identity Toolkit APIのURLを動的に構築
	url := fmt.Sprintf("https://identitytoolkit.googleapis.com/v2/projects/%s/tenants/%s?updateMask=passwordPolicyConfig.passwordPolicyConfig.passwordPolicyVersions", projectID, tenantID)

	requestBody, err := json.Marshal(map[string]interface{}{
		"passwordPolicyConfig": map[string]interface{}{
			"passwordPolicyVersions": []map[string]interface{}{
				{"customStrengthOptions": map[string]interface{}{"minPasswordLength": 10}},
			},
			"passwordPolicyEnforcementState": "ENFORCE",
			"forceUpgradeOnSignin":           false,
		},
	})
	if err != nil {
		return err
	}

	req, err := http.NewRequest("PATCH", url, bytes.NewBuffer(requestBody))
	if err != nil {
		return err
	}
	req.Header.Add("Authorization", "Bearer "+token)
	req.Header.Add("Content-Type", "application/json")

	client := &http.Client{}
	resp, err := client.Do(req)
	if err != nil {
		return err
	}
	defer resp.Body.Close()

	respBody, err := io.ReadAll(resp.Body)
	if err != nil {
		return err
	}

	fmt.Printf("Response: %s\n", string(respBody))
	return nil
}

```

:::message
APIの呼び出しの際は失敗することも想定して、下記のようなリトライする実装を組み込んでおくと良いでしょう。
@[card](https://zenn.dev/rescuenow/articles/70772dc9e3a27f)
:::

## 実装時のエラー
アクセストークン生成時の誤りによって発生するエラーが大半です。
401エラーで原因の特定が難しいです。

- **アクセストークンを生成したアカウントの誤り・権限不足**
```json
{
  "error": {
    "code": 403,
    "message": "Your application is authenticating by using local Application Default Credentials. The identitytoolkit.googleapis.com API requires a quota project, which is not set by default. To learn how to set your quota project, see https://cloud.google.com/docs/authentication/adc-troubleshooting/user-creds .",
    "status": "PERMISSION_DENIED",
    "details": [
      {
        "@type": "type.googleapis.com/google.rpc.ErrorInfo",
        "reason": "SERVICE_DISABLED",
        "domain": "googleapis.com",
        "metadata": {
          "consumer": "projects/｛プロジェクトID｝",
          "service": "identitytoolkit.googleapis.com"
        }
      }
    ]
  }
}
```

- **アクセストークンを生成したスコープ誤り**
```json
{
  "error": {
    "code": 401,
    "message": "Request is missing required authentication credential. Expected OAuth 2 access token, login cookie or other valid authentication credential. See https://developers.google.com/identity/sign-in/web/devconsole-project.",
    "status": "UNAUTHENTICATED",
    "details": [
      {
        "@type": "type.googleapis.com/google.rpc.ErrorInfo",
        "reason": "CREDENTIALS_MISSING",
        "domain": "googleapis.com",
        "metadata": {
          "method": "google.cloud.identitytoolkit.admin.v2.TenantManagementService.ListTenants",
          "service": "identitytoolkit.googleapis.com"
        }
      }
    ]
  }
}
```

## まとめ
Identity Platformの解説とマルチテナント構成でのパスワードポリシー更新について解説しました。
Identity Platformは、設定一つでマルチテナントの認証を実現できるため、認証周りの実装や、ユーザ管理を効率化できるため、非常に便利てす。
一方で、今回紹介したパスワードポリシーの更新などは、SDKやコンソールでの変更ができないため、手軽さの面では後一歩という感覚です。
個人的にはSDKに頼り切っていたので、いざアクセストークンの発行が必要となった時に意外と手間取りました。
SDKになく、APIのみ提供されている機能を利用する際には、同様にアクセストークンの発行が必要になるため、今後に活用できる知見が得られたのはよかったです。
SDKで完結できると余計な実装が減るので、今後のSDKアップデートを期待して終わりたいと思います。

## 参考資料
https://cloud.google.com/docs/authentication?hl=ja
https://cloud.google.com/docs/authentication/use-service-account-impersonation?hl=ja

パスワード ポリシーの有効化、無効化、使用
https://cloud.google.com/identity-platform/docs/password-policy?hl=ja
