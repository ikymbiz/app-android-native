
# ⚡ Native App Cloud Packager & Deployer

Webブラウザ上でHTML/CSS/JSなどのWebアセットをZIP圧縮し、直接GitHubへプッシュ。その後自動でGitHub Actionsをトリガーしてネイティブアプリ（Android APK等）のクラウドビルドを行い、結果を受け取るまでを完結させるワンストップWebツールです。

バックエンドサーバーは一切不要で、ブラウザとGitHub APIのみで動作します。

## ✨ 主な機能

- 📦 **ブラウザ内ローカル圧縮**: `JSZip` を使用し、ドラッグ＆ドロップしたファイルをクライアントサイドでZIP化 (`www.zip`) します。
- 🚀 **シームレスなデプロイ**: GitHub REST APIを利用し、ZIPファイルのプッシュからワークフローのトリガーまでをワンクリックで実行します。
- 🔄 **リアルタイム・ビルド監視**: GitHub Actionsの実行状況をポーリングし、画面内のコンソールで進捗をリアルタイムにお知らせします。
- 📥 **ダイレクトダウンロード**: ビルド成功時、GitHub Artifacts（生成されたAPKなど）へのダウンロードリンクを即座に表示します。
- 🔒 **セキュアな設計**: 入力されたGitHub PAT（Personal Access Token）はブラウザの `localStorage` のみに保存され、外部サーバーへは送信されません。

## 🛠️ アーキテクチャ

1. **[Client]** ユーザーがWebファイルをD&Dで選択。
2. **[Client]** メモリ上で `www.zip` を生成し、Base64エンコード。
3. **[GitHub API]** 対象リポジトリへ `www.zip` をコミット＆プッシュ。
4. **[GitHub API]** `workflow_dispatch` イベントを発火させ、アプリ名・パッケージIDを渡す。
5. **[Client]** Actionsの実行状況を数秒ごとにポーリング監視。
6. **[GitHub Actions]** クラウド上でネイティブビルドを実行し、Artifactを出力。

## 📋 必要な前提条件

このツールを使用するには、あらかじめ以下の準備が必要です。

1. **GitHub アカウント & リポジトリ**
2. **GitHub Personal Access Token (PAT)**
   - 推奨: **Fine-grained PAT**
   - 必要なスコープ:
     - `Contents`: **Read and Write** (ファイルのアップロード用)
     - `Actions`: **Read and Write** (ワークフローの実行と監視用)
3. **ターゲットリポジトリの Actions 設定**
   - リポジトリ内にビルド用のワークフローファイル (`.github/workflows/build-apk.yml`) が必要です。（詳細は後述）

## 🚀 使い方

1. このHTMLファイル (`index.html`) をブラウザで開きます。（ローカルファイルとして開くか、GitHub Pages等でホスティングしてください）
2. **GitHub Settings** セクションに以下を入力します。
   - GitHub PAT (保存ボタンで次回以降の入力を省略可能)
   - GitHub ユーザー名 または Organization名
   - 対象のリポジトリ名
3. **App Config** セクションに以下を入力します。
   - アプリの表示名 (例: `My Awesome App`)
   - パッケージID (例: `com.example.app`)
4. 画面中央のドロップゾーンに、アプリを構成するWebファイル（`index.html` 必須）をドラッグ＆ドロップします。
5. **「🚀 パッケージ & デプロイ」** ボタンをクリックします。
6. コンソールに進行状況が表示されます。ビルド完了後、ダウンロードリンクからアプリを取得してください！

## ⚙️ 必須: GitHub Actions 側の設定

このツールは、ターゲットリポジトリの `.github/workflows/build-apk.yml` を呼び出します。
リポジトリ側に以下のようなワークフローファイルを作成して配置してください。

```yaml
# .github/workflows/build-apk.yml の例 (Capacitorを使用したAndroidビルドのイメージ)
name: Build Native App

on:
  workflow_dispatch:
    inputs:
      app_name:
        description: 'App Name'
        required: true
        default: 'My App'
      app_id:
        description: 'App Package ID'
        required: true
        default: 'com.example.app'

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Code
        uses: actions/checkout@v3

      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'

      - name: Unzip Web Assets
        run: |
          mkdir -p www
          unzip -o www.zip -d www

      # --- ここに Capacitor や Cordova のビルドプロセスを記述 ---
      # 例: npm install, npx cap add android, npx cap copy, ./gradlew assembleDebug など
      
      - name: Upload APK Artifact
        uses: actions/upload-artifact@v3
        with:
          name: app-release
          # ビルドされたAPKのパスを指定してください
          path: android/app/build/outputs/apk/debug/app-debug.apk 
```
*(※ 上記は構成例です。実際のアプリのビルド環境（Capacitor, Cordova, React Nativeなど）に合わせて適宜カスタマイズしてください。)*

## ⚠️ 注意事項

- 対象ファイルの中に必ず `index.html` が含まれていることを確認してください（ツール内で警告が表示されます）。
- 巨大なファイルをアップロードしようとすると、ブラウザのメモリ制限やGitHub APIのペイロード制限（通常は100MB未満）に引っかかる可能性があります。大規模なアセットを扱う場合はご注意ください。
- GitHub Actionsの実行にはリポジトリの無料枠（または課金）が消費されます。

## 📄 ライセンス

MIT License

---
*Created with ❤️ for smooth web-to-native app deployments.*
```

### 💡 README作成のポイント
- **「なぜこのコードだけで動かないのか（依存関係）」を明確化**: ツールが送信する `workflow_dispatch` リクエストや `build-apk.yml` の呼び出しに対応するため、GitHubリポジトリ側にどのような `yml` ファイルを用意すべきかの具体例（サンプルコード）を提示しました。
- **アーキテクチャの明示**: HTMLソースコード内で実行されている「ZIP化 → API PUT → API POST(dispatch) → API GET(ポーリング)」という処理の流れを箇条書きにし、エンジニアがコードを読まずとも仕様を理解できるようにしました。
- **セキュリティの強調**: PAT（パスワード同等の権限）を扱うため、「サーバーレスであること」「`localStorage`のみに保存されること」を強調し、ユーザーの不安を払拭する構成にしました。