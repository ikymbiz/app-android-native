# ⚡ Native App Packager
[![HTML5](https://img.shields.io/badge/HTML5-E34F26?logo=html5&logoColor=white)](#)
[![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?logo=javascript&logoColor=black)](#)
[![Capacitor](https://img.shields.io/badge/Capacitor-119EFF?logo=capacitor&logoColor=white)](#)
[![GitHub Actions](https://img.shields.io/badge/GitHub_Actions-2088FF?logo=github-actions&logoColor=white)](#)

Webアプリ（HTML / CSS / JS）を、ブラウザ上の操作だけでネイティブAndroidアプリ（APK）に変換・パッケージングする完全サーバーレスなPWAツールです。
ビルド環境として **GitHub Actions** と **Capacitor** をフル活用し、環境構築なしで即座にアプリ化を実現します。

## ✨ 特徴 (Features)

- 🚀 **完全ブラウザ完結**: バックエンドサーバーは不要です。すべてブラウザからGitHub API経由で完結します。
- 📁 **選べるソースコード**: 
  - ローカルからWebアセット（フォルダ/ファイル）をドラッグ＆ドロップで一括読み込み。
  - 連携したGitHubリポジトリ内の特定ディレクトリ（`dist`や`build`など）から対象ファイルを直接選択。
- 🎨 **アプリ情報のカスタマイズ**: アプリ名、パッケージID、アプリアイコン（画像アップロード または アプリ名からの自動生成）を設定可能。
- 🤖 **CI/CDの完全自動化**: Capacitorの初期構成、Androidプロジェクトの生成、APKのビルドまでを行うGitHub Actionsワークフローを自動生成・実行。
- 📥 **ダイレクトダウンロード**: GitHub Actionsでのビルド完了を自動で検知し、画面上から直接APKをダウンロードできます。
- 🔒 **セキュア設計**: GitHubトークンなどの機密情報はブラウザの IndexedDB にのみ保存され、外部のサードパーティサーバーには一切送信されません。

## ⚙️ 仕組み (How it works)

1. **構成の決定**: アプリ情報と、パッケージングしたいWebアセットを選択します。
2. **ZIP化 & プッシュ**: 選択されたローカルファイルを `JSZip` でZIP化し、GitHub API経由で指定リポジトリにアップロードします（GitHubソース選択時はスキップ）。
3. **ワークフロー自動生成**: Capacitorの設定を含んだCI/CD定義ファイル（`build-apk.yml`）をリポジトリに生成・コミットします。
4. **クラウドビルド**: GitHub API（`workflow_dispatch`）をトリガーし、GitHub Actions上でJava環境・Capacitorをセットアップ、Android APKをビルドします。
5. **ポーリング監視**: ブラウザがActionsのステータスを監視し、成功時に成果物（Artifact）のダウンロードリンクを生成します。

## 📋 事前準備 (Prerequisites)

本ツールを使用するには、GitHubアカウントと以下の準備が必要です。

1. **GitHub Personal Access Token (PAT) の取得**:
   - GitHub設定からトークンを発行してください。
   - **Fine-grained PAT (推奨)**: 特定のリポジトリに対する `Contents`, `Actions`, `Workflows` の Read/Write 権限が必要です。
   - **Classic PAT**: `repo` および `workflow` スコープが必要です。
2. **作業用リポジトリの作成**:
   - ソースコードの一時保存と、GitHub Actionsを実行するための**空のリポジトリ**を作成しておいてください。（セキュリティとActions無料枠の観点から **Private** リポジトリを強く推奨します）

## 🚀 使い方 (Usage)

### 1. 初期設定 (GitHub連携)
1. 画面右上の歯車アイコン（⚙️）をクリックして設定画面を開きます。
2. 取得した GitHub トークン を入力し、「💾 トークンを保存して接続」をクリックします。
3. ユーザー名が自動取得されたら設定画面を閉じます。

### 2. アプリの構成 (App Configuration)
- **アプリの表示名**: ホーム画面に表示されるアプリ名を入力します。（`index.html` や `README.md` から自動推測されます）
- **パッケージID**: `com.example.myapp` のような一意識別子を入力します（自動生成ボタンあり）。
- **アプリアイコン**: 512x512のPNG画像を推奨します。設定しない場合は、テーマカラーのイニシャルアイコンが自動生成されます。

### 3. アセットの選択 (Web Assets)
- **ローカルからアップロード**: 
  - `index.html` を含むフォルダを点線エリアにドラッグ＆ドロップします。
- **GitHubから選択**:
  - 対象のリポジトリとディレクトリのパス（例: `/dist`）を指定し、「📂 ファイル一覧を取得」をクリック。パッケージに含めたいファイルにチェックを入れます。

### 4. パッケージ & デプロイ (Deploy)
1. 対象リポジトリを選択した状態で、「🚀 パッケージ & デプロイ」ボタンをクリックします。
2. コンソールに進行状況（デプロイログ）がリアルタイム表示されます。（※処理中は Wake Lock API によりスマホ・PCのスリープが防止されます）
3. 数分後、ビルドが完了すると「📥 APKをダウンロード」ボタンが表示されます。

## 🛠️ 使用技術 (Technologies)

- **Frontend**: HTML5, CSS3, Vanilla JavaScript
- **Libraries**: [JSZip](https://stuk.github.io/jszip/) (3.10.1)
- **Storage**: IndexedDB (PWA Ready)
- **Backend / CI/CD**: GitHub REST API, GitHub Actions
- **Mobile Framework**: [Ionic Capacitor](https://capacitorjs.com/) (v5)
- **Build Target**: Android (Node.js 24, Java 17)

## ⚠️ セキュリティとプライバシー (Security)

- **データ通信**: ブラウザと GitHub API (`api.github.com`) 間でのみ通信を行います。作成者や第三者のサーバーを経由することは一切ありません。
- **データ保存**: PAT（アクセストークン）や設定データは、お使いの端末のローカルデータベース（IndexedDB）にのみ保存されます。

## 📝 免責事項 (Disclaimer)

本ソフトウェアは「現状有姿（AS IS）」で提供されます。本ツールの使用によって生じたデータの損失、GitHub Actionsの無料利用枠の超過、その他いかなる損害についても、作者は一切の責任を負いません。GitHub Actionsの利用規約および制限事項を遵守してご利用ください。

---
*Created with ⚡ Native App Packager*