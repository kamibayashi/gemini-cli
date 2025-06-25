# Gemini CLIの実行とデプロイ

このドキュメントでは、Gemini CLIの実行方法と、Gemini CLIが使用するデプロイアーキテクチャについて説明します。

## Gemini CLIの実行

Gemini CLIを実行するにはいくつかの方法があります。どのオプションを選択するかは、Gemini CLIをどのように使用する予定かによって異なります。

---

### 1. 標準インストール（一般的なユーザーに推奨）

これは、エンドユーザーがGemini CLIをインストールするための推奨される方法です。NPMレジストリからGemini CLIパッケージをダウンロードします。

- **グローバルインストール:**

  ```bash
  # CLIをグローバルにインストール
  npm install -g @google/gemini-cli

  # これでどこからでもCLIを実行可能
  gemini
  ```

- **NPX実行:**
  ```bash
  # グローバルインストールなしでNPMから最新バージョンを実行
  npx @google/gemini-cli
  ```

---

### 2. サンドボックスでの実行（Docker/Podman）

セキュリティと分離のために、Gemini CLIはコンテナ内で実行できます。これは、副作用をもたらす可能性のあるツールをCLIが実行するデフォルトの方法です。

- **レジストリから直接:**
  公開されているサンドボックスイメージを直接実行できます。これは、Dockerしかなく、CLIを実行したい環境で役立ちます。
  ```bash
  # 公開されているサンドボックスイメージを実行
  docker run --rm -it us-docker.pkg.dev/gemini-code-dev/gemini-cli/sandbox:0.1.1
  ```
- **`--sandbox`フラグの使用:**
  Gemini CLIがローカルにインストールされている場合（上記の標準インストールを使用）、サンドボックスコンテナ内で実行するように指示できます。
  ```bash
  gemini --sandbox "ここにプロンプトを入力"
  ```

---

### 3. ソースからの実行（Gemini CLI貢献者に推奨）

プロジェクトの貢献者は、ソースコードから直接CLIを実行したいと思うでしょう。

- **開発モード:**
  この方法はホットリロードを提供し、アクティブな開発に役立ちます。
  ```bash
  # リポジトリのルートから
  npm run start
  ```
- **本番に近いモード（リンクされたパッケージ）:**
  この方法は、ローカルパッケージをリンクすることでグローバルインストールをシミュレートします。本番ワークフローでローカルビルドをテストするのに役立ちます。

  ```bash
  # ローカルのcliパッケージをグローバルのnode_modulesにリンク
  npm link packages/cli

  # これで`gemini`コマンドを使用してローカルバージョンを実行可能
  gemini
  ```

---

### 4. GitHubから最新のGemini CLIコミットを実行

GitHubリポジトリから直接、最新のコミット済みバージョンのGemini CLIを実行できます。これは、まだ開発中の機能をテストするのに役立ちます。

```bash
# GitHubのmainブランチから直接CLIを実行
npx https://github.com/google/gemini-cli
```

## デプロイアーキテクチャ

上記の実行方法は、以下のアーキテクチャコンポーネントとプロセスによって可能になります。

**NPMパッケージ**

Gemini CLIプロジェクトは、2つのコアパッケージをNPMレジストリに公開するモノレポです。

- `@google/gemini-cli-core`: バックエンド、ロジックとツール実行を処理します。
- `@google/gemini-cli`: ユーザー向けのフロントエンドです。

これらのパッケージは、標準インストールを実行するとき、およびソースからGemini CLIを実行するときに使用されます。

**ビルドとパッケージ化プロセス**

配布チャネルに応じて、2つの異なるビルドプロセスが使用されます。

- **NPM公開:** NPMレジストリに公開するために、`@google/gemini-cli-core`と`@google/gemini-cli`のTypeScriptソースコードは、TypeScript Compiler（`tsc`）を使用して標準のJavaScriptにトランスパイルされます。結果として得られる`dist/`ディレクトリが、NPMパッケージで公開されるものです。これは、TypeScriptライブラリの標準的なアプローチです。

- **GitHub `npx`実行:** GitHubから直接最新バージョンのGemini CLIを実行すると、`package.json`の`prepare`スクリプトによって異なるプロセスがトリガーされます。このスクリプトは`esbuild`を使用して、アプリケーション全体とその依存関係を単一の自己完結型JavaScriptファイルにバンドルします。このバンドルはユーザーのマシンでオンザフライで作成され、リポジトリにはチェックインされません。

**Dockerサンドボックスイメージ**

Dockerベースの実行方法は、`gemini-cli-sandbox`コンテナイメージによってサポートされています。このイメージはコンテナレジストリに公開され、プリインストールされたグローバルバージョンのGemini CLIが含まれています。`scripts/prepare-cli-packagejson.js`スクリプトは、公開前にこのイメージのURIをCLIの`package.json`に動的に挿入するため、`--sandbox`フラグが使用されたときにCLIがどのイメージをプルするかを把握できます。

## リリースプロセス

統一されたスクリプト`npm run publish:release`がリリースプロセスを調整します。このスクリプトは、次のアクションを実行します。

1.  `tsc`を使用してNPMパッケージをビルドします。
2.  CLIの`package.json`をDockerイメージURIで更新します。
3.  `gemini-cli-sandbox` Dockerイメージをビルドしてタグ付けします。
4.  Dockerイメージをコンテナレジストリにプッシュします。
5.  NPMパッケージをアーティファクトレジストリに公開します。 