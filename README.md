
# ⚡ Native App Packager

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)
[![HTML5](https://img.shields.io/badge/HTML5-E34F26?logo=html5&logoColor=white)](#)
[![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?logo=javascript&logoColor=black)](#)
[![GitHub Actions](https://img.shields.io/badge/GitHub_Actions-2088FF?logo=github-actions&logoColor=white)](#)
[![Capacitor](https://img.shields.io/badge/Capacitor-119EFF?logo=capacitor&logoColor=white)](#)

**Native App Packager** は、ブラウザ上で Webアセット (HTML / CSS / JavaScript) を選択するだけで、Android のネイティブアプリ (APK) を自動ビルドし、ダウンロードできる画期的なシングルページアプリケーション (SPA) です。

ローカル環境に **Node.js や Android Studio をインストールする必要はありません**。ビルドの重い処理はすべて GitHub Actions に委譲し、Capacitor を利用してクラウド上でネイティブ化を行います。

![App Screenshot](./docs/screenshot.png) *(※ スクリーンショットがある場合はパスを指定してください)*

## ✨ 主な機能

- 🛠 **ゼロ・ローカル環境構築**: ブラウザと GitHub アカウントさえあれば、すぐに Android アプリを作成可能です。
- 📱 **直感的なアプリ設定**: 
  - アプリ名、パッケージIDの設定（ID自動生成機能付き）。
  - カスタムアイコン画像のアップロード対応。未設定時はアプリ名から**美しいイニシャルアイコンを自動生成**します。
- 📁 **柔軟なソースコード選択**:
  - **ローカル**: フォルダのドラッグ＆ドロップ、複数ファイルのアップロード。
  - **GitHub**: 連携したリポジトリの特定パスからファイルを直接抽出。
- 🤖 **GitHub Actions フル自動化**: 
  - Capacitor を用いたビルド用ワークフロー (`build-apk.yml`) を自動生成・コミットし、即座に実行 (`workflow_dispatch`) します。
- 📊 **リアルタイムな進行状況監視**: コンソールUIでビルドログ風の進行状況を表示。バックグラウンドの Actions の状態を自動ポーリングします。
- 📥 **ダイレクトダウンロード**: ビルドが完了すると、生成された APK をブラウザからワンクリックでダウンロードできます。
- 💾 **ローカルファースト設計**: GitHub トークンや設定データはブラウザの `IndexedDB` に安全に保存され、次回以降の入力を省略します。
- 🌙 スリープ防止 (Wake Lock API) と PWA (Progressive Web App) に標準対応。

## 🏗 アーキテクチャと仕組み

1. **アセットの準備**: ブラウザ上で選択されたローカルファイルは `JSZip` によりメモリ上でZIP化されます。
2. **ソースの配置**: GitHub API を経由して、指定したリポジトリに Webアセットとビルド用のワークフロー YAML をプッシュします。
3. **クラウドビルド**: GitHub Actions が起動し、Ubuntu 環境上で Node.js / Java 17 をセットアップ後、[Capacitor](https://capacitorjs.com/) を初期化して Webアセットを Android プロジェクトに組み込みます。
4. **パッケージング**: Gradle が `.apk` をビルドし、GitHub Artifacts としてアップロードします。
5. **取得**: 本アプリが API を定期的に監視し、完了を検知次第 Artifacts から APK をダウンロードします。

## 📋 必要条件

本アプリを利用するには、以下の準備が必要です。

1. モダンな Webブラウザ (Chrome, Edge, Firefox, Safari 等)
2. **GitHub アカウント**
3. **GitHub Personal Access Token (PAT)**
   - トークンの生成はこちら: [GitHub Settings > Developer settings](https://github.com/settings/tokens)
   - 必要な権限 (Classic の場合): `repo` (コードのプッシュと Actions の実行のため)
   - *※ Fine-grained token を使用する場合は、対象リポジトリに対する Contents と Actions の Read/Write 権限が必要です。*
4. ビルドワークフローを実行するための **空の（または既存の）GitHub リポジトリ**。

## 🚀 使い方

### 1. 初期設定 (GitHub 連携)
1. 画面右上の歯車アイコン (⚙️) をクリックして設定画面を開きます。
2. 取得した GitHub トークン (PAT) を入力し、「💾 トークンを保存して接続」をクリックします。
3. 認証が成功すると、ユーザー名が自動取得され、使用可能なリポジトリ一覧が読み込まれます。

### 2. アプリ構成の設定
- **アプリの表示名**: ホーム画面に表示されるアプリ名を入力します。
- **パッケージID**: `com.yourname.app` のような形式で入力するか、「自動生成」ボタンを利用します。
- **アプリアイコン**: 512x512 の PNG 画像をアップロードします。（選択しない場合は、アプリ名に基づいたアイコンが自動生成されます）。

### 3. Web アセットの選択
ソースタイプを以下の2つから選択します。
- **ローカルからアップロード**: 
  - `index.html` を含むフォルダをドラッグ＆ドロップ、またはファイル選択ダイアログから選択します。
- **GitHubから選択**: 
  - 自分のリポジトリから特定のフォルダ（例: `/dist` や `/www`）を指定し、「ファイル一覧を取得」をクリックして対象ファイルを選択します。

### 4. デプロイとビルド
1. 「🚀 パッケージ & デプロイ」ボタンをクリックします。
2. コンソールに進行状況が表示されます。ブラウザのタブを閉じずにお待ちください（Wake Lock API によりスリープが防止されます）。
3. 完了後、「📥 APKをダウンロード」ボタンが出現するので、クリックしてアプリを入手します！

## 🛠 技術スタック

- **UI / Frontend**: HTML5, CSS3 (CSS Variables, Flexbox), Vanilla JavaScript
- **Web APIs**: `IndexedDB` (データ永続化), `WakeLock API` (スリープ防止), `FileReader API`
- **Libraries**: [JSZip](https://stuk.github.io/jszip/) (ファイル圧縮)
- **Backend / CI/CD**: GitHub REST API (`@octokit/rest` 相当の fetch 実装), GitHub Actions
- **Mobile Framework**: [Capacitor](https://capacitorjs.com/) v6, Android SDK, Gradle

## ⚠️ 注意事項と制限

- ビルドには GitHub Actions の無料枠（または有料枠）を消費します。プライベートリポジトリを利用する場合、Actions の無料利用可能時間（月に2000分等）にご注意ください。
- iOS (IPA) のビルドには対応していません（macOS ランナーが必要であり、署名プロセスが複雑なため）。
- 本ツールで生成された APK は `debug` ビルドです。Google Play ストアに公開するためには、別途キーストアを用いた署名（Release ビルド）が必要です。

## 📜 ライセンス

このプロジェクトは [MIT License](LICENSE) のもとで公開されています。自由に改変、再配布、商用利用が可能です。

---
*Created with ❤️ using HTML, JS, and GitHub Actions.*