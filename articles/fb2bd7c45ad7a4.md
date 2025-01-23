---
title: "Stripeのクーポンを使って割引を行う"
emoji: "📑"
type: "tech"
topics:
  - "php"
  - "api"
  - "stripe"
  - "サブスクリプション"
  - "クーポン"
published: true
published_at: "2024-03-12 08:00"
---

## はじめに
仕事でStripeを使ってクーポンを発行する機会がありました。
込み入った条件のクーポンを発行できるか調査しており、
頭の整理をかねてStripeのクーポンについてまとめたいと思います。
ここで扱うのは、サブスクリプションに適用するクーポンやプロモーションコードです。
1回限りの支払いに対するクーポンについては扱いません。

## Stripeについて
簡単に概要だけ解説します。
Stripeは、オンライン支払い処理を簡単にするプラットフォームです。
ビジネスがインターネット上で安全に決済を受け取ることが可能です。

- **シンプルなインターフェース**: Stripeは、使いやすくシンプルなインターフェースが特徴です。開発者向けAPIも提供されており、高度なカスタマイズも可能です。
- **幅広い決済手段**: Stripeは、クレジットカード、デビットカード、銀行振込、Apple Pay、Google Payなど、幅広い決済手段に対応しています。
- **グローバル展開**: Stripeは、世界200ヵ国近くの国で利用可能です。
- **高いセキュリティ**: Stripeは、PCI DSSなどのセキュリティ基準を満たしており、安全な決済環境を提供できます。
- **サブスクリプション機能**: Stripeは、サブスクリプション型のサービスに便利なサブスクリプション機能を提供しています。
- **手数料**： 月額基本料金はなく、決済手数料はやや高めで、他社3%前後に対して3.6%です。

## Stripeのクーポンについて
Stripeにおけるクーポンは、一般的なクーポン券と同様に、商品の価格を割引できる機能です。
クーポンを利用することで、商品の元の価格を変えることなく、クーポンを利用した顧客にのみ割引価格で販売することが可能です。

Stripeではクーポンとプロモーションコードという仕組みで割引機能を提供します。
クーポンで割引の種類、期間、引き換え可能な数量など大枠を決めます。
クーポンだけでも割引することは可能ですが、システム側で商品や顧客に対してクーポンを適用する必要があります。物理的なクーポン券のように顧客が任意のタイミングで利用して頂くためにはプロモーションコードを作成します。
プロモーションコードでは、クーポンをより細かな制限を付けて利用者を限定することが可能です。
一般的なクーポンはStripeでいうプロモーションコードがイメージに近いと言えます。
以降、クーポン、プロモーションコードという表現はStripeでのクーポン、プロモーションコードとして読み進めて下さい。

一例として、以下のような運用が考えられます。
1. 「初月無料」のクーポンを作成
2. 各月毎にプロモーションコードを作成
3. プロモーションコードでは、各月の期間に使用を制限


### クーポンの割引種類
大きく以下の2種類です。
- **固定額割引**: 購入金額から一定額を割り引く。
- **パーセンテージ割引**: 購入金額の特定のパーセンテージを割り引く。

それぞれ名前の通りですが、目的に応じて使い分けると良いでしょう。
**固定額割引**は、固定の金額を割り引くため、顧客にとっても提供する側にとっても値引きされる額が分かりやすいのが特徴ですが、商品の金額が高額な場合で値引き額が小さい場合、顧客にメリットを感じてもらえない可能性が高いです。
**パーセンテージ割引**は、反対に商品の金額に対して割引額が大きくなるため、顧客にメリットを感じて頂けるでしょう。また、商品の価格を変更した場合も一定割合の割引額となるため、利益率を一定にできます。

### 割引の価格
- **固定額割引**
最小金額：1円
最大金額：999,999,999,999円（約9,999億円　※日本円の場合）
固定額割引の場合、通貨毎に割引額を変えられます。
円では2,000円引き、ドルでは10ドル引き、のような指定が可能（現在は1ドル147円なので、日本円で購入した方がお得になるように見せる事ができます。）

- **パーセンテージ割引**
最小割引：0.01%
最大割引：100%

0円割引や、0パーセント割引のような割引が発生しないようなクーポンを作成することはできません。
初月無料のようなクーポンを作る際には、固定・パーセンテージどちらの割引方法でも可能ですが、私はパーセンテージ割引を用いて100％割引などで使用しています
パーセンテージの場合、商品の価格が変更になっても利用可能であり、他商品にも利用しやすいです。


### 多様な割引制限
特定の商品やサービス、顧客、期間、回数、金額など様々な条件を指定して適用可能です。

#### クーポンに対する制限
**クーポンの期間**
無期限、1回、複数月の3種類選択可能

- 無期限：サブスクリプションの契約している間常にクーポンが適用されます。
- 1回：サブスクリプション契約の初回の請求に対してのみ適用されます。
- 複数月：サブスクリプション契約から指定月数の請求に対してのみ適用されます。
例えば、複数月で4カ月を指定した場合、サブスクリプションの期間によって以下のような適用になります。
月次の場合、各月の請求に対して適用されます。つまり4回分の請求に適用されます。
年次の場合、初年度の請求に対して適用されます。
週次の場合、各週の請求に対して4カ月間適用されます。つまり1か月4週として、計16回分の請求に適用されます。

::: message
後ほど解説しますが、API でクーポンの期間を設定する場合には、
`duration`というパラメータが期間の設定に該当します。
無期限、1回、複数月はそれぞれ、`forever`、`once`、`repeating`という設定値です。
複数月の月数の指定は`duration_in_months`というパラメータに月数を指定します。
:::


**クーポンの引き換え回数制限**
- 顧客がクーポンを使用可能な期限の設定が可能
日付だけでなく時間指定まで可能
- クーポンの引き換え回数を制限可能

ここでの引き換え回数は引き換え回数制限は、顧客全体のクーポンに適用されます。
50回に制限した場合、同じ顧客が50回適用することもできますし、別々な顧客50人が1回ずつ適用することも可能です。
顧客に対して1回だけ使わせたい場合は、クーポンの回数制限は設定せず、プロモーションコードの設定で指定します。
クーポンの引き換え回数制限を1回とした場合、最初にクーポンを使用した一人だけが使用できるクーポンとなります。

特殊な例として、永続的に継続するような割引設定をしたクーポンで有効期限がある場合、そのクーポンを適用した顧客は永続的にその割引を受けることができます。
有効期限後に顧客がクーポンを適用することはできません。


#### プロモーションコードに対する制限
**初回注文の場合のみ使用可能**
顧客ごとに初めて購入する際にのみ使用可能
**特定の顧客に使用を限定**
事前に顧客をクーポンに紐づけることで、対象顧客のみにしか使用できないようにできます。
**コードの引き換え回数を制限**
プロモーションコードの使用回数を制限できます。
クーポンの引き換え回数制限をしている場合、その数を超えて設定はできません。
**有効期限を設定**
プロモーションコードの有効期限を制限できます。
クーポンの引き換え期限を設定をしている場合、その期限を超えて設定はできません。
**最小注文金額の設定**
商品の購入金額が一定上の場合に利用可能とする。
クーポンとは違いますが、2000円以上なら送料無料、のようなイメージ
税金を含める含めないの指定も可能


## 商品の販売方法によるクーポンの設定方法
以降は実際に商品にクーポンを適用するまでを解説していきます。
Stripeのドキュメントが詳しく書かれているので基本的にはそちらを参考にすれば設定は可能だと思います。
本記事では、StripeのAPI（特にPHPでのSDK使用時）を用いた場合にどういう動作になるかをメインに解説したいと思います。

### ドキュメントの見方
ドキュメントを見ていると以下のように「ダッシュボード」と「API」と書かれたタブになっている箇所がいくつかあります。
![](https://storage.googleapis.com/zenn-user-upload/e70214b7ec4b-20240309.png)

初期表示は図のように「ダッシュボード」の方が見えていますが、
「API」をクリックすることでAPIとしてのコード実装例を見ることができます。
![](https://storage.googleapis.com/zenn-user-upload/82e1dcaee0ba-20240309.png)
コードが表示されている右上の下矢印アイコンをクリックすると対応したコード例を表示してくれます。
StripeのAPIは様々な言語に対応しており、各言語用に`SDK`と呼ばれるAPIを便利に利用できるツールを提供しているため、SDKの実装例が表示されます。

![](https://storage.googleapis.com/zenn-user-upload/7ab685e5ecfe-20240309.png)
一番右にある実行アイコンをクリックすると、ページ下部にStripe Shellというコンソールが表示されます。
Stripe CLIのコマンドが書き出された状態になって表示されるので、そのままエンターキーでドキュメントで表示されている内容で実行した結果を確認することができます。
ここで実行した内容はStripeのテスト環境に反映されますので、安全に実行確認が可能です。
※実行した結果、検証環境に影響を与えることになるので、何が起きるか分からない場合は、ダッシュボードの手順で操作してみてどのページの何が変わるのかを確認したうえで実行するのがよいでしょう。

APIの詳細を知りたい場合、コードの関数部分にマウスカーソルを合わせると対応するAPIのリファレンスへのリンクが表示されます。
この機能を使うと簡単にAPIの詳細を確認できます。
![](https://storage.googleapis.com/zenn-user-upload/20598ab72d39-20240309.png)


## サブスクリプションの割引
https://docs.stripe.com/billing/subscriptions/coupons

### クーポンの作成
クーポンを作成します。
```php
// Set your secret key. Remember to switch to your live secret key in production.
// See your keys here: https://dashboard.stripe.com/apikeys
$stripe = new \Stripe\StripeClient('Stripeキー');

$stripe->coupons->create([
  'duration' => 'once',
  'id' => 'free-period',
  'percent_off' => 100,
]);
```

**APIパラメータの詳細**
オプションは指定しなくてもよいパラメータです。

**必須となるパラメータ**
設定 | 説明
--- | ---
percent_off または amount_off | パーセンテージ割引 か 固定額割引どちらかを指定する必要があります。
duration | クーポンの有効期間。once（1回）、forever（無期限）、repeating（複数月） のいずれか。複数月を指定した場合、duration_in_monthsのパラメータで月数を指定する必要がある。
duration_in_months | クーポンの有効期限で複数月を指定した場合必須。月数
currency | 固定額割引の場合必須。割引金額の通貨コード。3文字のISOコード
currency_options | 固定額割引の場合必須。異なる通貨で小計から差し引く金額。多通貨での販売時のルールに従う

**オプション**
設定 | 説明
--- | ---
id  | クーポンの一意のID。ダッシュボードで表示される名前です。すでに登録済みの名前は指定不可
max_redemptions | クーポンの最大引き換え回数
name | 請求書や領収書などに顧客に表示されるクーポンの名前。名前が未設定の場合IDが表示されます。
redeem_by | クーポンの利用期限を設定
applies_to | 特定の商品にのみクーポンを適用する場合に指定
metadata | メタデータ。クーポンの設定というよりはデータ管理上カテゴリ分けする目的で付けるタグのようなもの。

https://docs.stripe.com/api/coupons/create

### サブスクリプションにクーポンを適用する
作成したクーポンをサブスクリプションに適用します。
```php
// Set your secret key. Remember to switch to your live secret key in production.
// See your keys here: https://dashboard.stripe.com/apikeys
$stripe = new \Stripe\StripeClient('Stripeキー');

$stripe->subscriptions->create([
  'customer' => 'cus_4fdAW5ftNQow1a',
  'items' => [['price' => 'price_CBb6IXqvTLXp3f']],
  'coupon' => 'free-period',
]);
```

**必須パラメータとクーポンに関するパラメータ**

| パラメータ | 説明 |
|-------|------|
| customer | 必須。サブスクリプションする顧客のID |
| items | 必須。サブスクリプションする料金情報のリスト。最低限料金のIDを設定する。1サブスクリプションあたり最大20個まで持てる |
| coupon | サブスクリプションに適用されるクーポンID |
| promotion_code | サブスクリプションに適用するプロモーションコードのAPI ID |


APIの例ですが、このままでは動作しません。
パラメータに指定している顧客(customer)と商品の料金(itemsのprice)が存在しないIDを指定しているためです。
動作を確認するため、まずは顧客と料金を作成します。料金は商品を作る段階で作成されます。
ダッシュボードから必要な値を設定して作成します。

それぞれ赤枠の部分のIDを使用します。
**顧客**
![](https://storage.googleapis.com/zenn-user-upload/2edd75daa583-20240309.png)
**商品と料金**
![](https://storage.googleapis.com/zenn-user-upload/a71b81aefa07-20240309.png)
サブスクリプションの商品を作成する場合、「継続」を選択します。
請求期間は何でもよいですが、一般的な月次とします。
![](https://storage.googleapis.com/zenn-user-upload/6406c15f21a2-20240309.png)

顧客と料金が作れたら、APIの実行時にそれぞれのIDを作成したIDに差し替えて実行します。

:::details API実行結果　※長いので折り畳み
```json
{
  "id": "sub_1OsM9sFikfoKKsrjHqZzOAsP",
  "object": "subscription",
  "application": null,
  "application_fee_percent": null,
  "automatic_tax": {
    "enabled": false,
    "liability": null
  },
  "billing_cycle_anchor": 1709976336,
  "billing_cycle_anchor_config": null,
  "billing_thresholds": null,
  "cancel_at": null,
  "cancel_at_period_end": false,
  "canceled_at": null,
  "cancellation_details": {
    "comment": null,
    "feedback": null,
    "reason": null
  },
  "collection_method": "charge_automatically",
  "created": 1709976336,
  "currency": "jpy",
  "current_period_end": 1712654736,
  "current_period_start": 1709976336,
  "customer": "cus_PhlUvCVhPDCxIV",
  "days_until_due": null,
  "default_payment_method": null,
  "default_source": null,
  "default_tax_rates": [],
  "description": null,
  "discount": null,
  "ended_at": null,
  "invoice_settings": {
    "account_tax_ids": null,
    "issuer": {
      "type": "self"
    }
  },
  "items": {
    "object": "list",
    "data": [
      {
        "id": "si_Phlh1FbC3Rz7a3",
        "object": "subscription_item",
        "billing_thresholds": null,
        "created": 1709976336,
        "metadata": {},
        "plan": {
          "id": "price_1OsM4aFikfoKKsrjeQJASruq",
          "object": "plan",
          "active": true,
          "aggregate_usage": null,
          "amount": 1000,
          "amount_decimal": "1000",
          "billing_scheme": "per_unit",
          "created": 1709976008,
          "currency": "jpy",
          "interval": "month",
          "interval_count": 1,
          "livemode": false,
          "metadata": {},
          "nickname": null,
          "product": "prod_Phlb3znCji5wyN",
          "tiers_mode": null,
          "transform_usage": null,
          "trial_period_days": null,
          "usage_type": "licensed"
        },
        "price": {
          "id": "price_1OsM4aFikfoKKsrjeQJASruq",
          "object": "price",
          "active": true,
          "billing_scheme": "per_unit",
          "created": 1709976008,
          "currency": "jpy",
          "custom_unit_amount": null,
          "livemode": false,
          "lookup_key": null,
          "metadata": {},
          "nickname": null,
          "product": "prod_Phlb3znCji5wyN",
          "recurring": {
            "aggregate_usage": null,
            "interval": "month",
            "interval_count": 1,
            "trial_period_days": null,
            "usage_type": "licensed"
          },
          "tax_behavior": "unspecified",
          "tiers_mode": null,
          "transform_quantity": null,
          "type": "recurring",
          "unit_amount": 1000,
          "unit_amount_decimal": "1000"
        },
        "quantity": 1,
        "subscription": "sub_1OsM9sFikfoKKsrjHqZzOAsP",
        "tax_rates": []
      }
    ],
    "has_more": false,
    "total_count": 1,
    "url": "/v1/subscription_items?subscription=sub_1OsM9sFikfoKKsrjHqZzOAsP"
  },
  "latest_invoice": "in_1OsM9sFikfoKKsrj59qF3BIG",
  "livemode": false,
  "metadata": {},
  "next_pending_invoice_item_invoice": null,
  "on_behalf_of": null,
  "pause_collection": null,
  "payment_settings": {
    "payment_method_options": null,
    "payment_method_types": null,
    "save_default_payment_method": "off"
  },
  "pending_invoice_item_interval": null,
  "pending_setup_intent": "seti_1OsM9sFikfoKKsrjkZYEojhj",
  "pending_update": null,
  "plan": {
    "id": "price_1OsM4aFikfoKKsrjeQJASruq",
    "object": "plan",
    "active": true,
    "aggregate_usage": null,
    "amount": 1000,
    "amount_decimal": "1000",
    "billing_scheme": "per_unit",
    "created": 1709976008,
    "currency": "jpy",
    "interval": "month",
    "interval_count": 1,
    "livemode": false,
    "metadata": {},
    "nickname": null,
    "product": "prod_Phlb3znCji5wyN",
    "tiers_mode": null,
    "transform_usage": null,
    "trial_period_days": null,
    "usage_type": "licensed"
  },
  "quantity": 1,
  "schedule": null,
  "start_date": 1709976336,
  "status": "active",
  "test_clock": null,
  "transfer_data": null,
  "trial_end": null,
  "trial_settings": {
    "end_behavior": {
      "missing_payment_method": "create_invoice"
    }
  },
  "trial_start": null
}
```
:::

https://docs.stripe.com/api/subscriptions/create


サブスクリプションの更新時もクーポンを適用できます。
```php
$stripe = new \Stripe\StripeClient('Stripeキー');

$stripe->subscriptions->update(
  'sub_1MowQVLkdIwHu7ixeRlqHVzs',
  ['coupon' => 'free-period']
);
```

https://docs.stripe.com/api/subscriptions/update

**クーポンに関するパラメータ**
必須となるパラメータはサブスクリプションのID以外は特になくすべて任意パラメータです。

| パラメータ | 説明 |
|-------|------|
| coupon | サブスクリプションに適用されるクーポンID |
| promotion_code | サブスクリプションに適用するプロモーションコードのAPI ID |

:::details API実行結果　※長いので折り畳み
`discount`の`coupon`に更新したクーポンが設定されていることが分かります。

```json
{
  "id": "sub_1OsM9sFikfoKKsrjHqZzOAsP",
  "object": "subscription",
  "application": null,
  "application_fee_percent": null,
  "automatic_tax": {
    "enabled": false,
    "liability": null
  },
  "billing_cycle_anchor": 1709976336,
  "billing_cycle_anchor_config": null,
  "billing_thresholds": null,
  "cancel_at": null,
  "cancel_at_period_end": false,
  "canceled_at": null,
  "cancellation_details": {
    "comment": null,
    "feedback": null,
    "reason": null
  },
  "collection_method": "charge_automatically",
  "created": 1709976336,
  "currency": "jpy",
  "current_period_end": 1712654736,
  "current_period_start": 1709976336,
  "customer": "cus_PhlUvCVhPDCxIV",
  "days_until_due": null,
  "default_payment_method": null,
  "default_source": null,
  "default_tax_rates": [],
  "description": null,
  "discount": {
    "id": "di_1OsSMbFikfoKKsrj96chMZgl",
    "object": "discount",
    "checkout_session": null,
    "coupon": {
      "id": "free-period",
      "object": "coupon",
      "amount_off": null,
      "created": 1709970512,
      "currency": null,
      "duration": "once",
      "duration_in_months": null,
      "livemode": false,
      "max_redemptions": null,
      "metadata": {},
      "name": null,
      "percent_off": 100,
      "redeem_by": null,
      "times_redeemed": 2,
      "valid": true
    },
    "customer": "cus_PhlUvCVhPDCxIV",
    "end": null,
    "invoice": null,
    "invoice_item": null,
    "promotion_code": null,
    "start": 1710000189,
    "subscription": "sub_1OsM9sFikfoKKsrjHqZzOAsP"
  },
  "ended_at": null,
  "invoice_settings": {
    "account_tax_ids": null,
    "issuer": {
      "type": "self"
    }
  },
  "items": {
    "object": "list",
    "data": [
      {
        "id": "si_Phlh1FbC3Rz7a3",
        "object": "subscription_item",
        "billing_thresholds": null,
        "created": 1709976336,
        "metadata": {},
        "plan": {
          "id": "price_1OsM4aFikfoKKsrjeQJASruq",
          "object": "plan",
          "active": true,
          "aggregate_usage": null,
          "amount": 1000,
          "amount_decimal": "1000",
          "billing_scheme": "per_unit",
          "created": 1709976008,
          "currency": "jpy",
          "interval": "month",
          "interval_count": 1,
          "livemode": false,
          "metadata": {},
          "nickname": null,
          "product": "prod_Phlb3znCji5wyN",
          "tiers_mode": null,
          "transform_usage": null,
          "trial_period_days": null,
          "usage_type": "licensed"
        },
        "price": {
          "id": "price_1OsM4aFikfoKKsrjeQJASruq",
          "object": "price",
          "active": true,
          "billing_scheme": "per_unit",
          "created": 1709976008,
          "currency": "jpy",
          "custom_unit_amount": null,
          "livemode": false,
          "lookup_key": null,
          "metadata": {},
          "nickname": null,
          "product": "prod_Phlb3znCji5wyN",
          "recurring": {
            "aggregate_usage": null,
            "interval": "month",
            "interval_count": 1,
            "trial_period_days": null,
            "usage_type": "licensed"
          },
          "tax_behavior": "unspecified",
          "tiers_mode": null,
          "transform_quantity": null,
          "type": "recurring",
          "unit_amount": 1000,
          "unit_amount_decimal": "1000"
        },
        "quantity": 1,
        "subscription": "sub_1OsM9sFikfoKKsrjHqZzOAsP",
        "tax_rates": []
      }
    ],
    "has_more": false,
    "total_count": 1,
    "url": "/v1/subscription_items?subscription=sub_1OsM9sFikfoKKsrjHqZzOAsP"
  },
  "latest_invoice": "in_1OsM9sFikfoKKsrj59qF3BIG",
  "livemode": false,
  "metadata": {},
  "next_pending_invoice_item_invoice": null,
  "on_behalf_of": null,
  "pause_collection": null,
  "payment_settings": {
    "payment_method_options": null,
    "payment_method_types": null,
    "save_default_payment_method": "off"
  },
  "pending_invoice_item_interval": null,
  "pending_setup_intent": "seti_1OsM9sFikfoKKsrjkZYEojhj",
  "pending_update": null,
  "plan": {
    "id": "price_1OsM4aFikfoKKsrjeQJASruq",
    "object": "plan",
    "active": true,
    "aggregate_usage": null,
    "amount": 1000,
    "amount_decimal": "1000",
    "billing_scheme": "per_unit",
    "created": 1709976008,
    "currency": "jpy",
    "interval": "month",
    "interval_count": 1,
    "livemode": false,
    "metadata": {},
    "nickname": null,
    "product": "prod_Phlb3znCji5wyN",
    "tiers_mode": null,
    "transform_usage": null,
    "trial_period_days": null,
    "usage_type": "licensed"
  },
  "quantity": 1,
  "schedule": null,
  "start_date": 1709976336,
  "status": "active",
  "test_clock": null,
  "transfer_data": null,
  "trial_end": null,
  "trial_settings": {
    "end_behavior": {
      "missing_payment_method": "create_invoice"
    }
  },
  "trial_start": null
}
```
:::


### 顧客にクーポンを適用する
サブスクリプションではなく、顧客自体にクーポンを適用することも可能。
その顧客が持つすべてのサブスクリプションに適用され、クーポンを適用した後に追加したサブスクリプションにも同様にクーポンが適用されます。
一般的な顧客に適用するには難しそう。限定的な機能として利用できそうです。
- 管理用のアカウントとして費用が掛からないように割引クーポンで無料にする
- 早期購入特典として、永年20%引きにする

:::message
**割引の優先度**
1 つのインボイスに適用できるクーポンは 1 つだけです。
2 つ以上のクーポンを併用することはできません。
サブスクリプションに適用されるクーポンは、顧客に適用されるクーポンよりも優先されます。
:::

顧客作成時にクーポンを適用します。
```php
$stripe = new \Stripe\StripeClient('Stripeキー');

$stripe->customers->create([
  'name' => 'Jenny Rosen',
  'email' => 'jennyrosen@example.com',
  'coupon' => 'free-period',
]);
```

**必須パラメータとクーポンに関するパラメータ**
API的には必須パラメータはなし。
何も指定せず顧客を作成することも可能。名前未設定、email未設定な顧客が作成されます。

| パラメータ | 説明 |
|-------|------|
| coupon | サブスクリプションに適用されるクーポンID |
| promotion_code | サブスクリプションに適用するプロモーションコードのAPI ID |

:::details API実行結果　※長いので折り畳み
```json
{
  "id": "cus_PhsaVPG9FhMTGz",
  "object": "customer",
  "address": null,
  "balance": 0,
  "created": 1710001944,
  "currency": null,
  "default_currency": null,
  "default_source": null,
  "delinquent": false,
  "description": null,
  "discount": {
    "id": "di_1OsSouFikfoKKsrjcPRRNP29",
    "object": "discount",
    "checkout_session": null,
    "coupon": {
      "id": "free-period",
      "object": "coupon",
      "amount_off": null,
      "created": 1709970512,
      "currency": null,
      "duration": "once",
      "duration_in_months": null,
      "livemode": false,
      "max_redemptions": null,
      "metadata": {},
      "name": null,
      "percent_off": 100,
      "redeem_by": null,
      "times_redeemed": 4,
      "valid": true
    },
    "customer": "cus_PhsaVPG9FhMTGz",
    "end": null,
    "invoice": null,
    "invoice_item": null,
    "promotion_code": null,
    "start": 1710001944,
    "subscription": null
  },
  "email": "jennyrosen@example.com",
  "invoice_prefix": "B4187977",
  "invoice_settings": {
    "custom_fields": null,
    "default_payment_method": null,
    "footer": null,
    "rendering_options": null
  },
  "livemode": false,
  "metadata": {},
  "name": "Jenny Rosen",
  "next_invoice_sequence": 1,
  "phone": null,
  "preferred_locales": [],
  "shipping": null,
  "tax_exempt": "none",
  "test_clock": null
}
```
:::

https://docs.stripe.com/api/subscriptions/create


顧客更新APIも作成と同様にクーポンを適用できます。
```php
$stripe = new \Stripe\StripeClient('Stripeキー');

$stripe->customers->update(
  'cus_NffrFeUfNV2Hib',
  'coupon' => 'free-period',
]);
```

https://docs.stripe.com/api/subscriptions/update


### Checkoutにクーポンを適用する
CheckoutはStripeが提供するローコードの支払いフォームによる決済機能です。
Stripeのダッシュボードでは、「Payment Links」に該当します。
Checkoutを利用することで、ウェブサイトに直接埋め込んだり、Stripeが提供している決済ページへリダイレクトすることで決済を実現します。

Checkoutをクーポン付きで提供するためのAPI呼び出しです。
Checkoutセッション作成APIのパラメーターに`discounts`を設定します。

```php
// Set your secret key. Remember to switch to your live secret key in production.
// See your keys here: https://dashboard.stripe.com/apikeys
\Stripe\Stripe::setApiKey('Stripeキー');

$session = \Stripe\Checkout\Session::create([
  'payment_method_types' => ['card'],
  'line_items' => [[
    'price' => '{{PRICE_ID}}',
    'quantity' => 1,
  ]],
  'mode' => 'subscription',
  'discounts' => [[
    'coupon' => '{{COUPON_ID}}',
  ]],
  'success_url' => 'https://example.com/success',
  'cancel_url' => 'https://example.com/cancel',
]);
```

**必須パラメータとクーポンに関するパラメータ**

| パラメータ | 説明 |
|-------|------|
| line_items | セットアップモード以外では必須。顧客が購入している商品のリスト |
| mode | 必須。チェックアウトセッションのモード。`payment`：1回払い、`setup`：後払い、`subscription`：サブスクリプションの3種類 |
| return_url | 条件付きで必須。支払い認証後や、支払いキャンセルした後にリダイレクトするURL。`ui_mode`が`enbedded`でリダイレクトベースの支払い方法の場合に必須。 |
| success_url | 条件付きで必須。`ui_mode`が`enbedded`埋め込みの場合は利用不可。 |
| currency | 条件付きで必須。`mode`が`setup`で、`payment_method_types`が未設定の場合に必須 |
| payment_method_types | カード決済などの決済手段の種類 |
| discounts | クーポンかプロモーションコードを指定する。 |
| discounts.coupon | サブスクリプションに適用されるクーポンID |
| discounts.promotion_code | サブスクリプションに適用するプロモーションコードのID |

https://docs.stripe.com/api/checkout/sessions/create

#### Checkoutについて
https://docs.stripe.com/payments/checkout



### クーポンを削除する
クーポンを削除すると、その後のサブスクリプションや顧客に適用できなくなります。
そのクーポンを適用したサブスクリプションや顧客から割引が削除されることはありません。

```php
// Set your secret key. Remember to switch to your live secret key in production.
// See your keys here: https://dashboard.stripe.com/apikeys
$stripe = new \Stripe\StripeClient('Stripeキー');

$stripe->coupons->delete('free-period', []);
```

**結果**
```json
{
  "id": "2bl7i4ct",
  "object": "coupon",
  "deleted": true
}
```

https://docs.stripe.com/api/coupons/delete

### プロモーションコード
- クーポンに作成される顧客表示用のコード
- 特定の顧客、初回注文、最小注文値、有効期限、および引き換え回数制限などの制限を書けることができる
- 1つのクーポンに複数のプロモーションコードを生成できます。
- 任意の英数字でコードを作成できます。特に指定しない場合はランダムな値で生成されます。
- コードを無効にしたり、無効を解除することができます。

**制限**
プロモーションコードには以下のような制限があります。

- すでに作成した同じ値のコードを指定することはできません。無効にしたコードであれば、同じ値のコードで作成可能です。
- コードは大文字小文字を区別しません。そのため、大文字小文字が異なるだけのコードは同じコードとみなされ生成不可。例えば`test`と`TEST`というコードは同じコード扱いです。
- 顧客またはサブスクリプションの更新APIに、金額制限のあるプロモーションコードを適用することはできません。
- 特定のサブスクリプションや顧客に、プロモーションコードとクーポンの両方を同時に適用することはできません。
- 2 つ以上のプロモーションコードを適用することはできません。
- 一度作成したプロモーションコードの制限を変更することはできません。新しいコードを生成する必要があります。
    - コードを利用できる顧客を指定した場合、新しい顧客を含めることは不可
    - 初回のみ利用可にしたコードを、初回以外も利用可にすることは不可
    - 期限を指定したコードの期限を変更不可

### プロモーションコードを作成する
クーポンのIDを指定することでそのクーポンに紐づくプロモーションコードを作成できます。

```php
// Set your secret key. Remember to switch to your live secret key in production.
// See your keys here: https://dashboard.stripe.com/apikeys
$stripe = new \Stripe\StripeClient('Stripeキー');

$stripe->promotionCodes->create([
  'coupon' => 'ZQO00CcH',
  'code' => 'ALICE20',
  'customer' => 'cus_4fdAW5ftNQow1a',
]);
```

**結果**
```json

```

**パラメータ**

| パラメータ | 説明 |
|-------|------|
| coupon | 必須。クーポンID |
| code | プロモーションコード。顧客に見せる用のコード。指定しない場合はランダムな英数字で生成されます。 |
| metadata | メタデータ |
| active | プロモーションコードの有効無効設定。true：有効、false：無効 |
| customer | プロモーションコードを紐づける顧客のID。紐づけた顧客のみ利用可能。指定しない場合はすべての顧客が利用可能となります。 |
| expires_at | プロモーションコードの有効期限。クーポンに有効期限を設定している場合。それを超えた期限を設定できません。 |
| max_redemptions | プロモーション引き換え回数。クーポンの引き換え回数を設定している場合。それを超える値を設定できません。 |
| restrictions | プロモーションコードの引き換えを制限する設定値。構造体形式で制限項目を指定します。 |

**restrictions**
| パラメータ | 説明 |
|-------|------|
| currency_options | プロモーションコードを利用可能となる最低金額の指定。複数通貨の設定は定額割引のみ利用可能。通貨毎に指定可能で、このパラメータに対して構造体を設定する。3桁の通貨コードをキーに金額を指定します。※ |
| first_time_transaction | 顧客毎に初回の購入のみ適用するかどうかを指定。 |
| minimum_amount | プロモーションコードを利用可能となる最低金額の指定。 |
| minimum_amount_currency | プロモーションコードを利用可能となる最低金額の通貨コードを指定。 |

**※restrictionsの例**
- 最低100円以上の購入が必要な場合＋初回購入のみ制限
```json
{
  ・・・中略・・・
  "restrictions": {
    "first_time_transaction": true,
    "minimum_amount": 100,
    "minimum_amount_currency": "jpy"
  },
  "times_redeemed": 0
}
```

- 複数通貨の金額設定した場合。
  105円以上、0.05米ドル以上とした場合。
  ドルなど小数点を含む通貨は小数点なしで100倍された数値で取得するため、APIで取得した値を使用する場合は注意が必要です。
  例えば12.34ドルと設定した場合は1234という数値で返されます。
```json
{
  ・・・中略・・・
  "restrictions": {
    "currency_options": {
      "jpy": {
        "minimum_amount": 105
      },
      "usd": {
        "minimum_amount": 5
      }
    },
    "minimum_amount": 105,
    "minimum_amount_currency": "jpy"
  },
  "times_redeemed": 0
}
```

https://docs.stripe.com/api/promotion_codes/create

:::message
**レスポンス拡張について**
`currency_options`の項目は、APIでデータを取得した際、通常は結果に含まれません。
Expanding Responses（レスポンスの拡張）という仕組みを用いて、APIの実行時に明示的にレスポンス情報を拡張する指示を出すことで、結果に情報が含まれるようになります。
通常のAPIのレスポンスでは、あまり利用されないような情報を減らすことで、データ量を減らす事が目的だと考えられます。
どの項目がレスポンス拡張に該当するかはAPIのドキュメントで確認できます。
例えば、プロモーションコード作成APIの場合、'Returns'の部分で何を戻すかが記載されています。
たいていは何かのオブジェクト（構造体）の形で返すようになっています。
大抵は各APIの少し上の方にオブジェクト情報が記載されています。

プロモーションコード作成APIを例に解説します。
レスポンスはプロモーションコードオブジェクトです。
https://docs.stripe.com/api/promotion_codes/object

オブジェクトを構成する属性が沢山並んでいますが、項目の右側に`Expandable`と書かれているものがレスポンス拡張項目であり、通常のレスポンスには含まれない項目です。

プロモーションコード作成APIでは`customer`と`currency_options`がレスポンス拡張項目です。
これらの情報を全部欲しい場合に、データ取得API呼び出し時に`expand`というパラメータを付与することでレスポンス拡張項目も含めた形で結果が返されます。

```php
$stripe = new \Stripe\StripeClient("Stripeキー");
$stripe->promotionCodes->retrieve(
  'プロモーションコード',
  ['expand' => ['customer', 'restrictions.currency_options']]
);
```

@[card](https://docs.stripe.com/api/expanding_objects)
:::


## 小ネタ
‐ 定額割引で上限を超えた金額を設定した際のエラーメッセージ、通貨の部分が入力した金額になっていました。
![](https://storage.googleapis.com/zenn-user-upload/3ca3f693dd43-20240309.png)


## 参考資料
**サブスクリプションの割引**
https://docs.stripe.com/billing/subscriptions/coupons
**Stripe：料金体系**
https://stripe.com/jp/pricing
**Stripe：クーポンのAPI**
https://docs.stripe.com/api/coupons
**Stripe：Checkoutセッションについて**
https://docs.stripe.com/payments/checkout/how-checkout-works?locale=ja-JP
