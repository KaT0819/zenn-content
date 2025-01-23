---
title: "CloudflareでReactアプリをデプロイする"
emoji: "💭"
type: "tech"
topics:
  - "react"
  - "デプロイ"
  - "cloudflare"
published: true
published_at: "2024-07-08 20:05"
---


## はじめに
タイトルそのままですが、CloudflareでReactアプリをデプロイしていきます。

Cloudflareの公式に手順があるので、基本的な流れはそちらに沿って進めれば良いかと思います。
手順の中で、ところどころ入力が必要なので、その辺を中心に説明します。

### 手順
https://developers.cloudflare.com/pages/framework-guides/deploy-a-react-site/#deploy-via-the-create-cloudflare-cli-c3

## 事前準備
ローカル環境でnpmコマンドが使えるようにしておきます。
nodejsのインストールをすることでnpmコマンドが利用可能です。

## Claudflareのプロジェクト作成
`npm create`コマンドでプロジェクトを作成します。
```shell
npm create cloudflare@latest claude-app-sample -- --framework=react
```
「claude-app-sample」の部分は任意でプロジェクトの名前を指定します。
`--framework=react`でフレームワークを指定しており、ここでreactを指定することで、reactのアプリケーションの雛形が作成されます。

コマンドを実行したディレクトリの直下に指定した名前でディレクトリが作成され、その中にアプリケーションのファイルが作成されます。
例えば、`/user/app`というディレクトリで上記のコマンドを実行すると、
`/user/app/claude-app-sample`というディレクトリが作成されます。

以下コマンド実行後のコンソールログ
```shell
using create-cloudflare version 2.21.9

╭ Create an application with Cloudflare Step 1 of 3
│ 
├ In which directory do you want to create your application?
│ dir ./claude-app-sample
│
├ What type of application do you want to create?
│ type Website or web app
│
├ Which development framework do you want to use?
│ framework React
│
├ Continue with React via `npx create-react-app@5.0.1 claude-app-sample`
│

Need to install the following packages:
  create-react-app@5.0.1
Ok to proceed? (y) y
npm WARN deprecated inflight@1.0.6: This module is not supported, and leaks memory. Do not use it. Check out lru-cache if you want a good and tested way to coalesce async requests by a key value, which is much more comprehensive and powerful.
npm WARN deprecated glob@7.2.3: Glob versions prior to v9 are no longer supported
npm WARN deprecated uid-number@0.0.6: This package is no longer supported.
npm WARN deprecated fstream-ignore@1.0.5: This package is no longer supported.
npm WARN deprecated rimraf@2.7.1: Rimraf versions prior to v4 are no longer supported
npm WARN deprecated fstream@1.0.12: This package is no longer supported.
npm WARN deprecated tar@2.2.2: This version of tar is no longer supported, and will not receive security updates. Please upgrade asap.

Creating a new React app in /path/to/local/claude-app-sample.

Installing packages. This might take a couple of minutes.
Installing react, react-dom, and react-scripts with cra-template...


added 1483 packages in 2m

262 packages are looking for funding
  run `npm fund` for details

Initialized a git repository.

Installing template dependencies using npm...

added 63 packages, and changed 1 package in 20s

262 packages are looking for funding
  run `npm fund` for details
Removing template package using npm...


removed 1 package, and audited 1546 packages in 1s

262 packages are looking for funding
  run `npm fund` for details

8 vulnerabilities (2 moderate, 6 high)

To address all issues (including breaking changes), run:
  npm audit fix --force

Run `npm audit` for details.

Created git commit.

Success! Created claude-app-sample at /Users/krq2304-01/app/free/flare/claude-app-sample
Inside that directory, you can run several commands:

  npm start
    Starts the development server.

  npm run build
    Bundles the app into static files for production.

  npm test
    Starts the test runner.

  npm run eject
    Removes this tool and copies build dependencies, configuration files
    and scripts into the app directory. If you do this, you can’t go back!

We suggest that you begin by typing:

  cd claude-app-sample
  npm start

Happy hacking!

╰ Application created 

╭ Configuring your application for Cloudflare Step 2 of 3
│ 
├ Installing wrangler A command line tool for building Cloudflare Workers 
│ installed via `npm install wrangler --save-dev`
│ 
├ Adding Wrangler files to the .gitignore file 
│ updated .gitignore file
│ 
├ Updating `package.json` scripts 
│ updated `package.json`
│ 
├ Committing new files 
│ git commit
│ 
╰ Application configured 

╭ Deploy with Cloudflare Step 3 of 3
│ 
├ Do you want to deploy your application?
│ yes deploy via `npm run deploy`
```
ここで一度コンソールが止まってデプロイするか聞かれるので、一旦確認もかねて「Yes」でデプロイします。

```shell
│ yes deploy via `npm run deploy`
│
├ Logging into Cloudflare checking authentication status 
│ not logged in
│ 
├ Logging into Cloudflare This will open a browser window 
│ denied via `wrangler login`
│ 
├  APPLICATION CREATED  Deploy your application with npm run deploy
│ 
│ Navigate to the new directory cd claude-app-sample
│ Run the development server npm run dev
│ Preview your application npm run preview
│ Deploy your application npm run deploy
│ Read the documentation https://developers.cloudflare.com/pages
│ Stuck? Join us at https://discord.cloudflare.com
│ 
╰ See you again soon! 
```

デプロイ途中でブラウザに新しいページが立ち上がります。
![](https://storage.googleapis.com/zenn-user-upload/58a26d35a7c1-20240708.png)

WranglerがCloudflareのアカウントに変更を加えることを許可するかを聞かれています。
WranglerとはClaudeflareをコマンドで操作するためのCLI（コマンドラインインターフェース）です。

「Allow」で許可します。

※Allowをクリックするのが遅いとうまく動作しなくなることがあるので、表示されたらなるべく早めにクリックしてください。

![](https://storage.googleapis.com/zenn-user-upload/1f664f1cacbb-20240708.png)
「Allow」をクリックし、正しく許可できるとこのページが表示されます。
特に操作はないので、閉じてしまってOKです。

## ローカル実行
```shell
cd claude-app-sample
npm start
```

![](https://storage.googleapis.com/zenn-user-upload/20fbe61cb6bc-20240708.png)
自動でブラウザでページが表示されると思いますが、開かない場合は以下URLで開けます。
`http://localhost:3000`

また、すでに3000番ポートが別なアプリ等で使用されている場合、以下のようなメッセージがコンソールに表示されます。
```shell
? Something is already running on port 3000. Probably:
  /Applications/Docker.app/Contents/MacOS/com.docker.backend -watchdog -native-api (pid 873)
  in /path/to/local/Library/Containers/com.docker.docker/Data

Would you like to run the app on another port instead? › (Y/n)
```
他のポートを使用して良いかを聞かれているので、`y`を入力します。

そうすると別なポート番号でブラウザのページが表示されます。
例）`http://localhost:3001`


## デプロイ
プロジェクトを作成した時点でnpmのdeployコマンドでclaudeflareにデプロイされるように設定されています。

```shell
npm run deploy

> claude-app-sample@0.1.0 deploy
> npm run build && wrangler pages deploy ./build


> claude-app-sample@0.1.0 build
> react-scripts build

Creating an optimized production build...
One of your dependencies, babel-preset-react-app, is importing the
"@babel/plugin-proposal-private-property-in-object" package without
declaring it in its dependencies. This is currently working because
"@babel/plugin-proposal-private-property-in-object" is already in your
node_modules folder for unrelated reasons, but it may break at any time.

babel-preset-react-app is part of the create-react-app project, which
is not maintianed anymore. It is thus unlikely that this bug will
ever be fixed. Add "@babel/plugin-proposal-private-property-in-object" to
your devDependencies to work around this error. This will make this message
go away.
  
Compiled successfully.

File sizes after gzip:

  46.58 kB  build/static/js/main.21dae814.js
  1.78 kB   build/static/js/453.aca0cbda.chunk.js
  515 B     build/static/css/main.f855e6bc.css

The project was built assuming it is hosted at /.
You can control this with the homepage field in your package.json.

The build folder is ready to be deployed.
You may serve it with a static server:

  npm install -g serve
  serve -s build

Find out more about deployment here:

  https://cra.link/deployment

Attempting to login via OAuth...
Opening a link in your default browser: https://dash.cloudflare.com/oauth2/auth?response_type=code&client_id=54d11594-84e4-41aa-b438-e81b8fa78ee7&redirect_uri=http%3A%2F%2Flocalhost%3A8976%2Foauth%2Fcallback&scope=account%3Aread%20user%3Aread%20workers%3Awrite%20workers_kv%3Awrite%20workers_routes%3Awrite%20workers_scripts%3Awrite%20workers_tail%3Aread%20d1%3Awrite%20pages%3Awrite%20zone%3Aread%20ssl_certs%3Awrite%20constellation%3Awrite%20ai%3Awrite%20queues%3Awrite%20offline_access&state=G867Y4Dl_PCe~pQ4aT.1AbpH6IO.p0pe&code_challenge=Jn6i2Yak1e8Z_VnLRmgymQZCXBIu-a3ih6KV8im2vQg&code_challenge_method=S256
Successfully logged in.
✔ Enter the name of your new project: … claude-app-sample
```
ここで入力を求められます。
プロジェクトの名前を入力します。
cloudflareのダッシュボードなどで見える名前です。
最初にコマンドで実行した際につけた名前と一緒でよいでしょう。

```shell
✔ Enter the production branch name: … master
```
次にブランチの名前を指定します。デフォルトだと`master`です。
変える必要がなければそのままエンターで進みます。

```shell
✨ Successfully created the 'claude-app-sample' project.
✔ Would you like to help improve Wrangler by sending usage metrics to Cloudflare? … yes
Your choice has been saved in the following file: ../../../../Library/Preferences/.wrangler/metrics.json.

  You can override the user level setting for a project in `wrangler.toml`:

   - to disable sending metrics for a project: `send_metrics = false`
   - to enable sending metrics for a project: `send_metrics = true`
🌎  Uploading... (15/15)

✨ Success! Uploaded 15 files (11.05 sec)

🌎 Deploying...
✨ Deployment complete! Take a peek over at https://100f5aa5.claude-app-sample.pages.dev
```

コマンド入力が可能になるとデプロイが完了しています。
最後に表示されたURLにアクセスすることでデプロイされたアプリにアクセスできます。

※初めてデプロイを行った場合、すぐにページが表示されないこともあるので、うまく表示されない場合は数分待ってアクセスしてみてください。

作成したプロジェクトは、`Workers & Pages`の概要から確認できます。
![](https://storage.googleapis.com/zenn-user-upload/f4cf9d558f6b-20240708.png)

## おまけ：TypeScriptの使用
生成されたプロジェクトはjsファイルのみで構成されています。
typescriptを使用するためには、ライブラリを追加する必要があります。

詳細は以下公式を参考にしてください。
https://ja.react.dev/learn/typescript

### 既存の React プロジェクトに TypeScript を追加する
```shell
npm install @types/react @types/react-dom
```

### 設定ファイルの作成
`tsconfig.json`をルートディレクトリに作成し、設定項目を指定します。
```json
{
    "compilerOptions": {
        "target": "es5",
        "lib": [
            "dom",
            "dom.iterable",
            "esnext"
        ],
        "allowJs": true,
        "skipLibCheck": true,
        "esModuleInterop": true,
        "allowSyntheticDefaultImports": true,
        "strict": true,
        "forceConsistentCasingInFileNames": true,
        "noFallthroughCasesInSwitch": true,
        "module": "esnext",
        "moduleResolution": "node",
        "resolveJsonModule": true,
        "isolatedModules": true,
        "noEmit": true,
        "jsx": "react-jsx"
    },
    "include": [
        "src"
    ]
}
```

### App.jsをApp.tsxに変更

App.js
```
function MyButton() {
  return (
    <button>
      I'm a button
    </button>
  );
}

export default function MyApp() {
  return (
    <div>
      <h1>Welcome to my app</h1>
      <MyButton />
    </div>
  );
}
```

App.tsx
```
function MyButton({ title }: { title: string }) {
  return (
    <button>{title}</button>
  );
}

export default function MyApp() {
  return (
    <div>
      <h1>Welcome to my app</h1>
      <MyButton title="I'm a button" />
    </div>
  );
}
```
