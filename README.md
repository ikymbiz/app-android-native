# ⚡ Native App Packager

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](http://makeapullrequest.com)

**Native App Packager** は、ブラウザ上で完結する Web to Android App ビルドツールです。
ローカルに Android Studio や Node.js などの複雑な開発環境を構築することなく、HTML/CSS/JS の Web アセットから直接ネイティブアプリ（APK）を生成します。

バックエンドインフラとして **GitHub Actions** と **Capacitor** を活用し、すべてクラウド上でビルド処理を行います。

---

## ✨ 主な機能

- 🌐 **ブラウザ完結型ビルド**: ローカル環境の構築不要。ブラウザだけで設定からAPKダウンロードまで完結。
- ☁️ **クラウドビルドエンジン**: GitHub Actions を利用して、裏側で確実に Android アプリをビルド。
- 📁 **柔軟なソース選択**:
  - **ローカル**: PC内の HTML/CSS/JS フォルダをドラッグ＆ドロップで一括選択。
  - **GitHub**: 指定したリポジトリの特定ディレクトリからソースを直接取得。
- 🎨 **スマートなアイコン生成**: アプリ名から自動でアプリアイコンを生成する機能（カスタム画像のアップロードも可能）。
- 🤖 **メタデータ自動推論**: `index.html` の `<title>` や `README.md` からアプリ名を自動抽出。
- 📱 **PWA (Progressive Web App) 対応**: ツール自体をスマホやPCにインストールしてネイティブアプリのように利用可能。
- 🔒 **セキュアな設定保存**: GitHub Token などの設定はブラウザの `IndexedDB` に安全に保存。

---

## 🏗️ 仕組み (アーキテクチャ)

このツールは、以下のフローで動作します。

1. **設定・アセット準備**: ブラウザ上でアプリ設定（名前、ID、アイコン）と Web アセット（HTML/JS/CSS）を準備します。
2. **GitHub API 連携**: 準備したアセットを ZIP 化し、あなたの GitHub リポジトリへ API 経由でプッシュします。
3. **ワークフロー自動生成**: Android ビルド用の GitHub Actions ワークフロー（`.github/workflows/build-apk.yml`）を対象リポジトリに自動生成・コミットします。
4. **Capacitor によるビルド**: GitHub Actions がトリガーされ、クラウド上で `Capacitor` を用いて Web アセットを Android プロジェクトに変換し、APK をビルドします。
5. **ポーリング監視＆ダウンロード**: ブラウザから GitHub Actions の実行ステータスを監視し、成功すると生成された APK を直接ダウンロードします。

---

## 🚀 使い方

### 1. 前提条件
- **GitHub アカウント**
- **Personal Access Token (PAT)**: リポジトリの読み書き・Actions の実行権限（`repo`, `workflow`）を持つトークンが必要です。
- **デプロイ用リポジトリ**: ビルド処理を実行するための空の（または既存の）GitHubリポジトリ。

### 2. 初期セットアップ
1. 右上の歯車アイコン（⚙️）をクリックして**設定**を開きます。
2. GitHub PAT を入力し、「💾 トークンを保存して接続」をクリックします。
3. 認証が成功すると、あなたのリポジトリ一覧が取得できるようになります。

### 3. アプリケーションの設定
- **アプリの表示名**: ホーム画面に表示される名前です。
- **パッケージID**: `com.yourname.app` のような一意のIDを設定します（自動生成機能あり）。
- **アプリアイコン**: 512x512 の PNG 画像をアップロードできます。指定しない場合は自動生成されます。

### 4. ソースファイルの選択
- **ローカルからアップロード**: `index.html` を含むフォルダをドラッグ＆ドロップ、または選択します。
- **GitHubから選択**: 対象リポジトリとパス（例: `/dist`）を指定し、ファイルを取得・選択します。

### 5. パッケージ＆デプロイ
「🚀 パッケージ & デプロイ」ボタンをクリックします。
画面下部のコンソールにリアルタイムで進行状況（アップロード、アクションのトリガー、ビルド状況）が表示されます。

### 6. ダウンロード
ビルドが完了すると、「📥 APKをダウンロード」ボタンが出現します。クリックして Android 端末にインストールしてください。

---

## 🛠️ 使用技術

- **フロントエンド**: HTML5, CSS3, Vanilla JavaScript
- **ライブラリ**: [JSZip](https://stuk.github.io/jszip/) (クライアントサイドのZIP圧縮)
- **ビルドツール**: [Capacitor](https://capacitorjs.com/) (@capacitor/core, @capacitor/android)
- **CI/CD**: GitHub Actions, GitHub REST API
- **ストレージ**: IndexedDB API
- **その他**: Wake Lock API (ビルド中のスリープ防止)

---

## ⚠️ 注意事項と制限事項

- **`index.html` の必須性**: 選択したソース群のルート、または対象ディレクトリの直下に必ず `index.html` が存在する必要があります。存在しない場合、ビルドされたアプリが白画面になります。
- **GitHub Actions の利用枠**: 無料枠のアカウントを使用している場合、GitHub Actions の月間無料実行時間（通常2000分）を消費します。
- **トークンの管理**: GitHub PAT はブラウザのローカルデータベース(IndexedDB)にのみ保存され、外部サーバーへは送信されません。ただし、共有PCでの利用には注意してください。

---

## 📄 ライセンス

このプロジェクトは [MIT License](LICENSE) のもとで公開されています。自由に改変・再配布が可能です。