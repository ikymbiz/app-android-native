# ⚡ Native App Packager

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![HTML5](https://img.shields.io/badge/HTML5-E34F26?logo=html5&logoColor=white)](#)
[![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?logo=javascript&logoColor=black)](#)
[![Capacitor](https://img.shields.io/badge/Capacitor-119EFF?logo=capacitor&logoColor=white)](#)
[![GitHub Actions](https://img.shields.io/badge/GitHub_Actions-2088FF?logo=github-actions&logoColor=white)](#)

ブラウザ上の操作だけで、あなたのWebアプリケーション（HTML/CSS/JS）を**Androidネイティブアプリ（APK）に自動変換**する完全なフロントエンドツールです。

ローカル環境での Node.js や Android Studio のセットアップは一切不要。GitHub Actions の強力なクラウドビルド環境を利用して、Webブラウザから直接 Android アプリを生成・ダウンロードできます。

## ✨ 主な機能

- **📱 環境構築ゼロ**: Webブラウザさえあれば、PC・スマホ問わずどこからでもビルドを開始できます。
- **📦 柔軟なアセット入力**:
  - **ローカル**: フォルダのドラッグ＆ドロップで一括アップロード。
  - **GitHub**: 既存リポジトリから特定のパス・ファイルを選択してインポート。
- **🎨 アプリアイコン＆ID管理**: アプリ名からのアイコン自動生成機能や、カスタム画像のアップロードに対応。パッケージIDもワンクリックで自動生成。
- **☁️ GitHub Actions 自動化**: Capacitor v6 を利用したビルド用ワークフローファイル（`build-apk.yml`）を対象リポジトリへ自動生成・配置し、ビルドをトリガーします。
- **🔍 リアルタイム監視**: 画面上のコンソールでビルドの進行状況やGitHub Actionsのステータスをリアルタイムにポーリング監視。
- **📥 ダイレクトダウンロード**: ビルド完了後、生成されたAPKファイルをブラウザから直接ダウンロード可能。
- **🔒 安全なローカル保存**: GitHubトークンや設定情報は、ブラウザの `IndexedDB` にのみ保存されます。
- **🌐 PWA対応**: Progressive Web Appとしてデバイスにインストール可能です（Wake Lock APIにも対応し、ビルド中のスリープを防止）。

## 🏗️ 仕組み (How it works)

本ツールはバックエンドサーバーを持ちません。すべてブラウザ上で完結し、以下のフローで動作します：

1. **設定**: Web UI上でアプリ名、アイコン、パッケージIDを設定。
2. **ソース選択**: ローカルファイル、またはGitHubリポジトリからWebアセット（`index.html` 等）を選択。
3. **デプロイ準備**: ブラウザがGitHub APIを使用し、ファイルをZIP化（ローカルの場合）して対象リポジトリにプッシュ。同時にビルド用のYAMLワークフローをコミットします。
4. **クラウドビルド**: GitHub Actions が自動的に起動し、`Capacitor` を用いてWebアセットをAndroidアプリにラップ・コンパイルします。
5. **ダウンロード**: Web UIがビルドの完了を検知すると、ActionsのArtifacts（成果物）からAPKを直接ダウンロードします。

## 📋 前提条件

このツールを使用するには、以下が必要です。

- **GitHubアカウント**
- **GitHub Personal Access Token (PAT)**
  - 必要な権限 (Classic Token の場合): `repo` (リポジトリの読み書き) および `workflow` (Actionsの実行)
  - または同等の権限を持つ Fine-grained Token
- **作業用のGitHubリポジトリ**（ビルドを実行するための空リポジトリ、または既存リポジトリ）

## 🚀 使い方

### Step 1: 初期設定 (GitHub連携)
1. 画面右上の ⚙️ (設定) アイコンをクリックします。
2. 取得した **GitHub PAT** を入力し、「💾 トークンを保存して接続」をクリックします。
3. ユーザー名が自動取得されたら設定画面を閉じます。

### Step 2: アプリの設定
1. **アプリの表示名** を入力します。
2. **パッケージID** を入力または「自動生成」ボタンで設定します（例: `com.example.myapp`）。
3. 必要に応じて **アプリアイコン** をアップロードします（未設定の場合はアプリ名から自動生成されます）。

### Step 3: Webアセット (www) の選択
**ローカルからアップロードする場合:**
- フォルダを選択するか、点線エリアにHTML/CSS/JSファイルをドラッグ＆ドロップします。
- `index.html` が含まれていることを確認してください。

**GitHubから選択する場合:**
- 「GitHubから選択」のラジオボタンをオンにします。
- 対象のリポジトリとパス（例: `/dist` や `/`）を指定し、「📂 ファイル一覧を取得」をクリックして対象ファイルを選択します。

### Step 4: ビルドとダウンロード
1. 画面下部の **「🚀 パッケージ & デプロイ」** ボタンをクリックします。
2. 画面下の **Deployment Logs** に進行状況が表示されます。ブラウザを閉じずにお待ちください（通常数分かかります）。
3. ビルドが成功すると、**「📥 APKをダウンロード」** リンクが出現します。クリックしてアプリを手に入れましょう！

## 🛠️ 使用技術

- **UI / ロジック**: HTML5, CSS3, Vanilla JavaScript
- **ブラウザ API**: IndexedDB, Wake Lock API, File System Access API
- **依存ライブラリ**: [JSZip](https://stuk.github.io/jszip/) (ローカルファイルのZIP圧縮)
- **ビルドシステム**: GitHub Actions (Ubuntu), [Ionic Capacitor](https://capacitorjs.com/), Android SDK (Java 17)

## ⚠️ 注意事項・制限事項

- **Android専用**: 現在生成されるワークフローは Android (APK) のビルドのみに対応しています。iOSアプリ（IPA）のビルドには macOS ランナーと Apple Developer プログラムの証明書が必要なため、本ツールではサポート外です。
- **ファイルサイズ制限**: GitHub APIの制限により、極端に大容量な動画やアセットを含むWebアプリのローカルアップロード（Base64エンコードを含むAPI経由でのプッシュ）は失敗する可能性があります。大規模なアプリの場合は「GitHubから選択」モードの使用を推奨します。
- **ブラウザの互換性**: フォルダのアップロード機能（`webkitdirectory`）はモダンブラウザ（Chrome, Edge, Safari等）でのみ動作します。

## 📜 ライセンス

このプロジェクトは [MIT License](LICENSE) の下で公開されています。ご自由に改変・再配布してご活用ください。

---
*Created with ⚡ Native App Packager*