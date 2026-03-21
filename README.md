# ⚡ Native App Packager
[![HTML5](https://img.shields.io/badge/HTML5-E34F26?style=flat&logo=html5&logoColor=white)]()
[![Vanilla JS](https://img.shields.io/badge/JavaScript-F7DF1E?style=flat&logo=javascript&logoColor=black)]()
[![GitHub Actions](https://img.shields.io/badge/GitHub_Actions-2088FF?style=flat&logo=github-actions&logoColor=white)]()

**Native App Packager** は、HTML/CSS/JS で構成された Web プロジェクトを、ブラウザ上の操作だけで簡単に Android アプリ (APK) に変換できるサーバーレスな Web アプリケーションです。

ローカルに Android Studio や Node.js などの開発環境を構築する必要はありません。すべてのビルドプロセスは **GitHub Actions** 上で [Capacitor](https://capacitorjs.com/) を用いて自動的に実行されます。

## ✨ 主な特徴 (Features)

*   🚀 **完全サーバーレス**: 自前のバックエンドサーバーは不要。GitHub Actions をクラウドビルドサーバーとして利用します。
*   📁 **柔軟なソース選択**:
    *   **ローカルファイル**: ドラッグ＆ドロップでフォルダごと追加し、ブラウザ内でZIP圧縮してアップロード。
    *   **GitHubリポジトリ**: 既存のリポジトリ内にある特定ディレクトリ（`dist`や`/`など）からファイルを直接指定してビルド。
*   ⚙️ **自動環境構築**: GitHub Actions のワークフローファイル (`build-apk.yml`) をアプリが自動生成し、デプロイ先リポジトリに配置します。
*   ⏱️ **リアルタイム監視**: ビルドステータスを自動ポーリングし、ブラウザ上のコンソールに進行状況を表示します。
*   📱 **PWA & Wake Lock 対応**: インストール可能な PWA として動作。ビルド待機中にデバイスがスリープするのを防ぐ Wake Lock API に対応しています。
*   🔒 **セキュアな設計**: 入力した GitHub Token や設定情報は、ブラウザの `IndexedDB` にのみ安全にローカル保存されます。

## 🏗️ アーキテクチャ (How it works)

本アプリケーションは以下のフローで動作します：

1.  **フロントエンド**: ユーザーがアセットを選択し、アプリ名やパッケージIDを設定。
2.  **API通信**: ブラウザから GitHub REST API を直接叩き、アセット (ZIP) と自動生成した `build-apk.yml` を対象リポジトリへ Push します。
3.  **ビルド起動**: `workflow_dispatch` イベントを発火させ、GitHub Actions をトリガーします。
4.  **クラウドビルド**: Actions ランナー上で Node.js, Java, Capacitor がセットアップされ、Android APK がビルドされます。
5.  **取得**: フロントエンドが Actions のステータスを監視し、成功すると Artifacts から APK のダウンロードリンクを提示します。

## 🛠️ 事前準備 (Prerequisites)

このツールを使用するには、以下の GitHub 関連の準備が必要です。

1.  **デプロイ用リポジトリの作成**:
    ビルド処理を実行し、ソースコードを一時的に保存するための空のリポジトリ（Public または Private）を GitHub 上に作成します。
2.  **GitHub Token (PAT) の取得**:
    *   GitHub の [Developer settings](https://github.com/settings/tokens) から **Personal Access Token** を作成します。
    *   必要な権限 (スコープ): `repo` (コードのPush/Pull用), `workflow` (Actionsの実行用)

## 🚀 使い方 (Usage)

### 1. 初期設定
1. 画面右上の歯車アイコン (⚙️) をクリックして「設定」を開きます。
2. 取得した **GitHub Token** を入力し、「💾 トークンを保存して接続」をクリックします。
3. 接続に成功するとリポジトリ一覧が取得されるので、「**デプロイ先リポジトリ**」を選択して設定画面を閉じます。

### 2. アプリの設定
*   **アプリの表示名**: スマートフォンにインストールされた際に表示される名前 (例: `My App`)。
*   **パッケージID**: アプリの一意の識別子 (例: `com.example.myapp`)。

### 3. Webアセットの選択
以下のいずれかの方法で、アプリの中身となる HTML/CSS/JS を指定します。
*   **ローカルからアップロード**: 開発中のフォルダを点線エリアにドラッグ＆ドロップします。（※ルートに `index.html` が含まれている必要があります）
*   **GitHubから選択**: 別途ホスティングしているリポジトリから、ビルド対象のディレクトリ（例: `/dist`）を指定し、ファイルを取得・選択します。

### 4. パッケージ＆デプロイ
1. 「**🚀 パッケージ & デプロイ**」ボタンをクリックします。
2. 画面下部のコンソールに進行状況が表示されます。
3. ビルドが完了すると「成功」ステータスになり、**APK ダウンロードリンク** が表示されます。リンク先の GitHub Artifacts ページから APK をダウンロードして実機にインストールしてください。

## 💻 技術スタック (Tech Stack)

*   **Frontend**: HTML5, CSS3 (CSS Variables, Flexbox), Vanilla JavaScript
*   **Libraries**: [JSZip](https://stuk.github.io/jszip/) (ブラウザ内での高速なZIP生成)
*   **Storage**: IndexedDB (設定の永続化)
*   **APIs**: GitHub REST API, Web Wake Lock API
*   **CI/CD (Backend)**: GitHub Actions (Ubuntu-latest, Node 24, Java 17, Capacitor 5)

## ⚠️ セキュリティとプライバシーに関する免責事項

*   **トークンの取り扱い**: 本アプリケーションは、入力された GitHub Personal Access Token (PAT) を外部のサーバーに送信することはありません。データはすべてブラウザのローカルストレージ (`IndexedDB`) に保存され、GitHub API との直接通信にのみ使用されます。
*   **自己責任での利用**: 本ツールは利便性を目的として提供されています。PAT の流出には十分注意し、不要になったトークンは GitHub 上で速やかに取り消し(Revoke) を行ってください。

## 📜 ライセンス (License)

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---
*Created with ❤️ for Web Developers who want to go Native instantly.*