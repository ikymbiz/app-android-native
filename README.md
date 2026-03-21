
# ⚡ Native App Packager

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)
[![HTML5](https://img.shields.io/badge/HTML5-E34F26?style=flat&logo=html5&logoColor=white)]()
[![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?style=flat&logo=javascript&logoColor=black)]()
[![GitHub Actions](https://img.shields.io/badge/GitHub_Actions-2088FF?style=flat&logo=github-actions&logoColor=white)]()

**Webアプリケーション(HTML/CSS/JS)をブラウザ上の操作だけでAndroidネイティブアプリ(APK)に変換するツールです。**

専用のバックエンドサーバーは一切不要。あなたのGitHubリポジトリとGitHub Actionsを活用して、クラウド上でセキュアかつ自動的にCapacitorビルドを実行します。

![App Screenshot](./screenshot.png) <!-- 必要に応じてスクリーンショットのパスを変更してください -->

## ✨ 主な機能 (Features)

- **🖥️ 完全ブラウザ完結 (SPA)**
  - 複雑な環境構築は不要。ブラウザを開くだけですぐに利用可能です。
  - PWAに対応しており、デスクトップやモバイル端末にインストールしてネイティブアプリのように使えます。
- **📁 柔軟なアセット選択**
  - **ローカルから:** フォルダごとドラッグ＆ドロップでアップロード可能。
  - **GitHubから:** リポジトリ内の特定のディレクトリやファイルを選択して取り込むことが可能。
- **⚙️ 自動構成・自動生成**
  - `index.html` や `README.md` を解析し、アプリ表示名を自動取得。
  - アプリ名に基づいたデフォルトの「ダイナミック・アプリアイコン」を自動生成（カスタム画像のアップロードも可能）。
  - ランダムハッシュ付きのパッケージID（`com.example.app.xxxx`）をワンクリック生成。
- **☁️ クラウドビルド (Powered by GitHub Actions)**
  - アプリのソースコードと、Androidビルド用のワークフローファイル (`build-apk.yml`) を自動生成してリポジトリにプッシュ。
  - 自動的にCapacitorを利用したビルドプロセスをトリガー。
- **👀 リアルタイム監視と直接ダウンロード**
  - GitHub Actionsの実行状況をブラウザ上のコンソールでリアルタイムにポーリング監視。
  - ビルド成功後、生成されたAPKファイルをブラウザから直接ダウンロードできます。
- **🔒 セキュアなデータ管理**
  - GitHub APIトークン（PAT）や設定内容はブラウザの `IndexedDB` にのみ保存され、外部のサードパーティサーバーに送信されることはありません。

## 🏗️ アーキテクチャと仕組み (How it works)

1. **構成のパッケージング**: ブラウザ上でJSZipを利用し、選択されたWebアセットをZIP化します。
2. **GitHub API連携**: 設定されたトークンを使用して、指定したリポジトリにZIPファイルとビルド用のGitHub Actionsワークフローファイルを直接コミットします。
3. **ワークフロー実行 (`workflow_dispatch`)**: GitHub ActionsをAPI経由でトリガーします。
4. **クラウドビルド (Capacitor)**: Actions上でNode.jsとJava環境がセットアップされ、アセットを展開後、Capacitorを用いてAndroidプロジェクト(`APK`)がビルドされます。
5. **アーティファクトの取得**: ビルド完了後、GitHub ActionsのArtifactsからAPKを自動で取得し、ダウンロードリンクを提示します。

## 📋 前提条件 (Prerequisites)

このアプリを利用するには以下の準備が必要です。

1. **GitHubアカウント**
2. **作業用のGitHubリポジトリ** (空のリポジトリでも可)
3. **GitHub Personal Access Token (PAT)**
   - トークンにはリポジトリの読み書き (`repo` スコープ、または Fine-grained token で Contents と Actions の Read/Write 権限) が必要です。

## 🚀 使い方 (Getting Started)

### 1. GitHub連携の設定
1. 画面右上の歯車アイコン (⚙️) をクリックして設定を開きます。
2. **GitHub PAT** を入力し、「💾 トークンを保存して接続」をクリックします。
3. 認証が成功すると自動的にユーザー名が取得・保存されます。

### 2. アプリの基本設定
1. **アプリの表示名** と **パッケージID** を入力します。
2. **アプリアイコン** を設定します（画像を選択しない場合は自動生成されます）。

### 3. Webアセットの選択
- **ローカルの場合**: 「フォルダを選択」またはドラッグ＆ドロップで、`index.html` を含むWebプロジェクトのフォルダを読み込ませます。
- **GitHubの場合**: 「対象リポジトリ」と「アセットのパス」を入力し、「📂 ファイル一覧を取得」をクリックして対象のファイルを選択します。

### 4. パッケージとデプロイ
1. 「🚀 パッケージ & デプロイ」ボタンをクリックします。
2. コンソール（Deployment Logs）に進行状況が表示されます。ブラウザを閉じずにお待ちください（※スリープ防止機能が自動で働きます）。
3. ビルドが成功すると、コンソール下部に「📥 APKをダウンロード」ボタンが出現します。

## 🛠️ 技術スタック (Tech Stack)

- **Frontend**: HTML5, CSS3 (CSS Variables), Vanilla JavaScript
- **Libraries**: [JSZip](https://stuk.github.io/jszip/) (ZIPアーカイブ生成)
- **APIs**: GitHub REST API v3, Web Storage API (IndexedDB), Screen Wake Lock API
- **Build Tool (Cloud)**: GitHub Actions, Node.js, Java, [Capacitor](https://capacitorjs.com/)

## ⚠️ 注意事項

- ビルド対象のWebアセットには、必ずルートまたは指定ディレクトリ直下に `index.html` が含まれている必要があります。
- 無料枠のGitHub Actionsを利用する場合、使用時間制限にご注意ください。
- 大量のファイルや極端に巨大な動画・画像ファイルをアップロードすると、ブラウザのメモリ制限やGitHub APIの制限に引っかかる可能性があります。

## 📄 ライセンス (License)

このプロジェクトは [MIT License](LICENSE) のもとで公開されています。自由に複製、改変、配布が可能です。
```

### 💡 README作成のポイント・工夫した点
1. **視覚的な魅力アップ:** バッジ（Shields.io）と絵文字を適度に使用し、モダンで親しみやすいドキュメントにしました。
2. **ユーザーの不安を払拭:** バックエンドサーバーが存在せず、GitHubの機能だけで完結する点や、トークンがIndexedDBに安全に保存される仕組み（セキュリティ面）を強調しました。
3. **仕組み（How it works）の明文化:** フロントエンドのコードからどのようにしてAndroid APKが生まれるのか、その魔法のようなプロセスの種明かしをステップバイステップで記述しました。
4. **分かりやすい導入手順:** ユーザーが迷わずにセットアップからビルド完了まで進めるよう、UIに沿った手順を記載しました。