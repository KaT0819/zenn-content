---
title: "ChatGPTを自分仕様にできるGPTsの紹介"
emoji: "📚"
type: "tech"
topics:
  - "chatgpt"
  - "gpts"
published: true
published_at: "2024-01-30 15:27"
publication_name: "rescuenow"
---

## はじめに

今回はカスタマイズしたオリジナルのChatGPTを作成できるGPTsと、他のユーザが作成したGPTsを検索できるサービス「GPT Store」について紹介します。
GPTsを利用すると、ユーザーはChatGPTの機能を自身の特定の用途に合わせてカスタマイズし、最適化することができます。
GPTsは2023年11月に提供され、3か月弱しかたっていませんが、すでに300 万を超えるGPTsが作成されているようです。
本記事では、GPTsの基本概念、利点、設定方法、および具体的な使用例に焦点を当てて解説します。


## GPTsの特徴と基本概念

### GPTsの主な特徴

1. **ユーザー定義のプロンプトとドキュメント**:
   - ユーザーが指定した指示と関連ドキュメントを使って、ChatGPTの応答をカスタマイズできます。
   - これにより、特定のタスクや目的に特化したオリジナルのChatGPTを作成することが可能になります。

2. **カスタマイズのプロセス**:
   - GPTsの作成はノーコードプロセスで、プログラミングの専門知識は必要ありません。
   - ユーザーは、指示を与えて、特定の機能やアクションをChatGPTに組み込むこめます。

3. **柔軟性**:
   - GPTsは、ユーザーのプロンプトに基づいて特定の出力を提供するよう設計されています。
   - 応答する表現のニュアンスを専門家風にしたり、カジュアルにしたりといった調整が可能です。
     応答する内容を限定的にしたり、フォーマットを特定の形式に指定することもできます。

4. **共有性**:
   - GPTsは作成者自身が使用するだけでなく、他のユーザーとも共有可能です。

### GPTsの基本概念

- **カスタマイズされた対話**:
  - GPTsを使用することで、ユーザーは特定の話題やタスクに集中した対話を実現できます。
- **多様な応用**:
  - GPTsは、教育、ビジネス、エンターテインメントなど、幅広い分野で応用可能です。

### GPTsの利用方法

- **GPTsを利用するための条件**:
  - ChatGPTの有料プランに加入している必要があります。
  - 有料プランに加入していない場合、GPTsを作成することはできなませんし、他の人が作成したGPTsを使用することもできません。
    いずれは無料のユーザも利用が可能になるかもしれませんが、現時点では利用できないため残念な点です。

- **有料プランの種類**:

	| プラン名       | 対象    | 価格        | 備考        |
	|--------------|----------|-------------|--------------|
	| ChatGPT Plus      | 個人利用向け | 月額 20ドル|  |
	| ChatGPT Team      | 複数人向け | 月額 30ドル/人 | 2名以上から利用可能 |
	| ChatGPT Enterprise | 企業向け | 要問い合わせ | |

	※料金以外の違いとして、GPT4の使用回数やトークン数など制限がプランによって異なります。

https://openai.com/chatgpt/pricing


- **GPTsを作成して使用**:
  - ユーザーは、特定のインタラクションや会話フローを設計し、それに基づいてGPTsを構築します。

- **GPT Storeから探して使用**:
  - GPT Storeから他のユーザが公開しているGPTsを利用することができます。

## GPTsの利点

### 利点

GPTsは、多くの利点を持っています。これにより、ChatGPTをよりパーソナライズし、特定の目的に合わせて効率的に活用することが可能になります。

1. **パーソナライズされたインタラクション**:
   - ユーザーは、特定のニーズや問い合わせに応じたカスタマイズされた応答を設計できます。これにより、ChatGPTの応答がより関連性が高く、有用になります。
   - 通常のChatGPTにおいても、「カスタム指示」の使用や会話の最初に指示を行うことで、同様の事ができていましたが、すべてのチャットで共通であるため、振る舞いを変えたい場合には書き換えて使う必要がありました。また、「カスタム指示」はアカウントで1つしか設定できず、使い分けする場合、テキストを別に保管しておくなど不便な点がありました。
     GPTsであれば、作成したGPTs毎に振る舞いを変えられるため、この不便さを解消できます。
   - ファイルを添付することで、そのファイルから情報を整理して応答することも可能です。
     企業が有する情報をGPTsに持たせることで、サポートデスク的な振る舞いも可能です。

2. **効率の向上**:
   - 特定のタスクやプロセスを自動化することで、作業の効率を大幅に向上させることができます。たとえば、ロゴや画像の生成、メール返信文の作成、データ分析、レポート作成などが挙げられます。
   - エンジニア領域においては、わざわざGPTs化しなくてもある程度判別してくれますが、特定の言語やバージョン、アーキテクチャ構成を指定しておいたり、新しいAPIやライブラリなどは公式ドキュメントを検索するように指示するなど特化させることで、GPTとのやり取りを効率化できます。

3. **共有**:
   - 作成したGPTsは他のユーザーと共有できるため、知識やツールをチームやコミュニティと共有することが可能です。
   - 共有の種類は3種類で、自分のみ、リンクを知る人、一般公開となっています。
   - 一般公開したGPTsはGPT Store内で検索可能な状態になります。
   - チームプランでは作成したGPTsはチーム内で共有できます。

### 応用例

1. **教育用ツール**:
   - GPTsは、特定の教科やトピックに特化した教育ツールとして使用できます。例えば、数学や科学の問題解決、言語学習など。

2. **ビジネスプロセスの自動化**:
   - レポートの自動生成、顧客サービスのためのFAQ応答の自動化、データ分析など、ビジネスプロセスを効率化するためにカスタマイズされたGPTsを活用できます。

3. **個人的なアシスタント**:
   - 日々のスケジュール管理、リマインダー設定、パーソナルタスクの管理など、個人的な用途に特化したGPTsを作成できます。


## GPTs作成前の準備

### セットアッププロセス

1. **サブスクリプションの確認**:
   - GPTsを使用するには、ChatGPT Plus、Team、またはEnterpriseプランに加入している必要があります。
   - 加入していない場合は、まず、必要なプランに登録することから始めます。

2. **ChatGPTにログイン**:
   - ChatGPTのウェブサイトにアクセスし、アカウントでログインします。

3. **GPTs作成ページへ移動**:
	- ダッシュボード左のリストから「GPTを探索する」をクリック
	![](https://storage.googleapis.com/zenn-user-upload/78c73794b32b-20240128.png)

	- GPT Storeのページが開くので、ページ右上の「GPTを作成する」でGPTs作成ページが開きます。
	![](https://storage.googleapis.com/zenn-user-upload/9029293be21a-20240128.png)


### GPTs作成の手順

GPTsの作成方法は大きく2つあります。

1. GPT Builderを用いてチャットでやり取りしながら作成・・・カンタンに作成
2. GPTsの名前や振る舞いばどを自分で設定して作成・・・細かいチューニングが可能

#### GPT Builderを用いてチャットでやり取りしながら作成
1. **GPTの作成**:
   - 「GPTを作成する」ボタンをクリックし、新しいカスタムGPTの作成を開始します。
   - 「Create」というタブをクリックしてチャットの入力欄に作成したい内容を入力していきます。

![](https://storage.googleapis.com/zenn-user-upload/2dfc94cad8d3-20240128.png)

画面の左がGPT Builderとチャットしていく部分です。右は作成されていくGPTsをプレビュー表示しています。
出来上がったGPTsに質問して挙動を確認することができます。

2. **GPT BuilderによるGPT作成の流れ**:
   - すべてチャット上でGPT Builderが質問をしてきた内容に答えていくことでGPTsが出来上がっていきます。
   - GPTsにやらせたいことを入力した後の大まかな流れは以下です。
   1. GPTsの名前を決める
   2. GPTsのイメージ画像を決める
   3. やらせたいことの具体化（特定の分野、領域、種類、問題の解消などを指定）
   4. 応答ニュアンスの調整（シンプルか、詳細か）
   5. 会話のスタイル（固い表現かフレンドリーか）
   - 判断に迷う場合は「おまかせ」と言っておけば、よしなに設定してくれます。

  - GPT Builderとのやりとりは日本語でできますが、GPT Builderからの応答は基本的に英語で返されます。「日本語にして」、とメッセージを送れば直前の応答を日本語にしてくれますが、以降のやりとりを日本語で返してくれません。

3. **共有と公開の設定**:
   - 作成したGPTを自分だけで使用するか、他のユーザーと共有するか、公開するかを選択します。
   - 公開を選択すると、GPT Storeで他のユーザーに提供することができます。
   - 「確認」ボタンをクリックして忘れずに保存しましょう。


#### GPTsの名前や振る舞いばどを自分で設定して作成
1. **GPTの作成**:
   - 「GPTを作成する」ボタンをクリックし、新しいカスタムGPTの作成を開始します。
   - 必要に応じて指示や機能を追加し、GPTの性質を定義します。

   ![](https://storage.googleapis.com/zenn-user-upload/02755c4a47f9-20240128.png)

2. **プロンプトとドキュメントの設定**:
   - GPTに必要なプロンプトやドキュメントを追加し、応答をカスタマイズします。
   - 応答のスタイルやフォーマットを指定することもできます。

3. **追加機能の選択**:
   - 必要に応じて、Webブラウジング、画像生成、コード解釈などの機能を追加します。

4. **共有と公開の設定**:
   - 作成したGPTを自分だけで使用するか、他のユーザーと共有するか、公開するかを選択します。
   - 公開オプションを選択すると、GPT Storeで他のユーザーに提供することができます。

このセクションでは、GPTsの基本的なセットアップとカスタマイズ方法について解説しました。次のセクションでは、具体的なGPTsの使用例に焦点を当てます。

## GPTsの具体的な使用例

GPTsを活用することで、様々なシナリオやニーズに応じたGPTsがGPT Storeにあります。

以下のカテゴリーに分類されています。
- DALL·E：画像生成系
- Writing：文章生成系
- Productivity：生産性向上（便利ツール）
- Research & Analysis：データ分析系
- Programming：プログラミング系
- Education：教育系
- Lifestyle：ライフスタイル（趣味、エンタメ系）

### DALL·E：画像生成系
1. [Logo Creator](https://chat.openai.com/g/g-gFt1ghYJl-logo-creator) - By Chase Lean
   - あなたのアイデアを素晴らしい画像に変換します。
2. [image generator](https://chat.openai.com/g/g-pmuQfob8d-image-generator) - By NAIF J ALOTAIBI
   - プロフェッショナルでフレンドリーなトーンで画像を生成・洗練するGPT。
3. [Cartoonize Yourself](https://chat.openai.com/g/g-gFFsdkfMC-cartoonize-yourself) - By karenxcheng.com
   - 写真をピクサースタイルのイラストに変換します。写真をアップロードして試してみてください。

### Writing：文章生成系
1. [Write For Me](https://chat.openai.com/g/g-B3hgivKK9-write-for-me) - By puzzle.today
   - 品質、関連性、正確な単語数に焦点を当てた、オーダーメイドで魅力的なコンテンツを作成します。
2. [Humanizer Pro](https://chat.openai.com/g/g-2azCVmXdy-humanizer-pro) - By charlygpts.com
   - 市場でNo.1のヒューマナイザー。AI検出を避けながら人間のようなテキストを書きます。
3. [✏️All-around Writer (Professional Version)](https://chat.openai.com/g/g-UbpNAGYL9-all-around-writer-professional-version) - By Kevin Ivery
   - あらゆるタイプのコンテンツ（エッセイ、小説、記事、コピーライティング）を専門とするプロのライター。

### Productivity：生産性向上（便利ツール）
1. [Canva](https://chat.openai.com/g/g-alKfVrz9K-canva) - By canva.com
   - プレゼンテーション、ロゴ、ソーシャルメディアの投稿など、何でも簡単にデザイン。
2. [Diagrams: Show Me](https://chat.openai.com/g/g-5QhhdsfDj-diagrams-show-me) - By helpful.dev
   - 図、アーキテクチャのビジュアライゼーション、フローチャート、マインドマップ、スキームなどを作成。
3. [Video GPT by VEED](https://chat.openai.com/g/g-Hkqnd7mFT-video-gpt-by-veed) - By veed.io
   - AIビデオメーカー。YouTube、Instagram、TikTokなどのソーシャルメディア用の動画を生成。

### Research & Analysis：データ分析系
1. [Consensus](https://chat.openai.com/g/g-bo0FiWLY7-consensus) - By consensus.app
   - あなたのAIリサーチアシスタント。200M以上の学術論文から検索し、科学に基づいた回答を得て、正確な引用を伴うコンテンツを作成。
2. [AskYourPDF Research Assistant](https://chat.openai.com/g/g-UfFxTDMxq-askyourpdf-research-assistant) - By askyourpdf.com
   - AIを使ってリサーチを自動化。複数のファイル（無制限のPDF）とチャットし、有効な引用を含む記事/エッセイを生成。
3. [ScholarAI](https://chat.openai.com/g/g-L2HknCZTC-scholarai) - By scholarai.io
   - AI科学者 - 2億以上の研究論文や書籍からテキスト、図、表を分析し、新しい仮説を生成。

### Programming：プログラミング系
1. [Grimoire](https://chat.openai.com/g/g-n7Rs0IK86-grimoire) - By gptavern.mindgoblinstudios.com
   - コーディングの魔法使い🧙‍♂️ プロンプトでプログラミングを学ぶ！文章でウェブサイト（または何でも）を作成。
2. [DesignerGPT](https://chat.openai.com/g/g-2Eo3NxuS7-designergpt) - By Pietro Schirano
   - 美しいウェブサイトを作成し、ホストします。
3. [💻Professional Coder (Auto programming)](https://chat.openai.com/g/g-HgZuFuuBK-professional-coder-auto-programming) - By Kevin Ivery
   - プログラミングの問題を解決するエキスパート。

### Education：教育系
1. [CK-12 Flexi](https://chat.openai.com/g/g-cEEXd8Dpb-ck-12-flexi) - By flexi.org
   - 中学生と高校生のための最も強力な数学と科学のAIチューター。
2. [Universal Primer](https://chat.openai.com/g/g-GbLbctpPz-universal-primer) - By runway.com
   - 何でも、すべてについて学ぶ。
3. [Video Summarizer](https://chat.openai.com/g/g-GvcYCKPIH-video-summarizer) - By Klym Zhuravlov-Iuzefovych
   - YouTube動画：長い動画から教育的な要約を生成します。

### Lifestyle：ライフスタイル・趣味・エンタメ系
1. [KAYAK - Flights, Hotels & Cars](https://chat.openai.com/g/g-hcqdAuSMv-kayak-flights-hotels-cars) - By kayak.com
   - フライト、ホテル、車の旅行計画アシスタント。
2. [Books](https://chat.openai.com/g/g-z77yDe7Vu-books) - By Josh Brent N. Villocido
   - 文学と読書の世界でのあなたのAIガイド。
3. [Astrology Birth Chart GPT](https://chat.openai.com/g/g-WxckXARTP-astrology-birth-chart-gpt) - By authorityastrology.com
   - 専門家の占星術GPT。質問に答えるためにあなたの出生情報が必要です。


### 個人的に使用しているもの
自分でGPTsを作っている中で試行錯誤していると使用制限がかかってしまうため、実は他の人のGPTsをあまり使用することが出来ていないのが現状です。
個人プランの場合、３時間あたり40回メッセージという制限があり、制限がかかるとかなりの時間待つことになるため、気軽に試すのを躊躇してしまいます。

そんなわけで、お試し程度にしか使っていない中で、比較的よさそうだったものをいくつか挙げたいと思います。

- Write For Me
Writingカテゴリでランキングされていますが、同じようなGPTsの中では使える印象でした。
‐ Logo Creator
こちらもちょっとしたロゴを作りたい時に使えそうです。


### 私が作成したもの
1. [スタディ侍（Study Samurai）](https://chat.openai.com/g/g-IaCjMSGVd-sutadeishi-study-samurai) 
   - 中学受験の問題解説用に作成しました。テキストだけでなく、問題の画像をメッセージに添付すれば解説してくれます。図形は苦手です。
	![](https://storage.googleapis.com/zenn-user-upload/97a0ded84e27-20240130.png =250x)

2. [ブログアシスタント林](https://chat.openai.com/g/g-Tbipi0qp7-buroguasisutantolin) 
   - ブログ作成用に作りました。キャラクタ性を重視したあまり、ブログ記事のテキストまで口調が反映されてしまうおちゃめな奴です。
	![](https://storage.googleapis.com/zenn-user-upload/c11551d0d66b-20240130.png =250x)

3. [Suno Lyricist AI](https://chat.openai.com/g/g-om13XuNvm-suno-lyricist-ai) 
   - 音楽生成AIのsuno AI用の歌詞生成用に作りました。
	![](https://storage.googleapis.com/zenn-user-upload/5401903fcf16-20240130.png =250x)
	**suno.aiで実際に出来上がった曲**
	https://app.suno.ai/song/6c8120ee-4099-40cc-a3a1-5e6111fb9021

4. [確定申告の税務相談](https://chat.openai.com/g/g-Km2zYgBm4-que-ding-shen-gao-noshui-wu-xiang-tan) 
   - 壁打ち用に作成してみました。確定申告に必要そうな法律を把握していますが、あまり表に出さない。能ある鷹は爪を隠すタイプ。
   ![](https://storage.googleapis.com/zenn-user-upload/726b31da6e40-20240130.png =250x)


## GPTsの今後の展望

1. **より高度なカスタマイズ**:
   - 今後のアップデートでは、GPTsのカスタマイズオプションがさらに拡張されることが期待されています。より複雑なタスクや特殊なニーズに対応できるようになるでしょう。
   - 最近のアップデートで通常チャットからGPTsを呼び出せるようになりました。
     これにより、複数のGPTsを1つのチャットで使用し、組み合わせることが容易になり、その利用が促進されるでしょう。
   - このアップデートは今後のGPTsの方向性を決めるかもしれないです。
     組み合わせが容易になったことで、1つ1つのGPTsはより単機能で単純で精度の高い回答をさせつつ、回答を次のGPTsにつなげて最終応答を生成するようなGPTsチェインのような使い方が出てくるでしょう。（単機能の方が色々なチャットに使われそうですし。）

2. **収益化**:
   - GPTsの使用に応じて作者は収入が得られるとのこと。有益なGPTsを作成することで、思わぬ利益が生まれる可能性があります。

	https://openai.com/blog/introducing-the-gpt-store


## 結論

本記事では、GPTsの概要、作成方法、およびGPT Storeについて解説しました。

GPTsは、ChatGPTを自分たちのニーズに合わせてカスタマイズする強力なツールです。
GPT Storeもまだまだ発展途上なため、ランキングも毎週新しいGPTsが登場して入れ替わっているような雰囲気です。
GPTs使用の制限が厳しいので制限緩和されないと、使用感を試すこともやり辛い状況なので、
GPT Store今後の発展とともに利用制限緩和を期待しつつ終わりたいと思います。
