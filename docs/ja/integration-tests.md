# 統合テスト

このドキュメントでは、このプロジェクトで使用されている統合テストフレームワークに関する情報を提供します。

## 概要

統合テストは、Gemini CLIのエンドツーエンドの機能を検証するために設計されています。制御された環境でビルドされたバイナリを実行し、ファイルシステムと対話する際に期待どおりに動作することを確認します。

これらのテストは`integration-tests`ディレクトリにあり、カスタムテストランナーを使用して実行されます。

## テストの実行

統合テストは、デフォルトの`npm run test`コマンドの一部としては実行されません。`npm run test:integration:all`スクリプトを使用して明示的に実行する必要があります。

統合テストは、次のショートカットを使用して実行することもできます。

```bash
npm run test:e2e
```

## 特定のテストセットの実行

テストファイルのサブセットを実行するには、`npm run <integration test command> <file_name1> ....`を使用します。ここで、`<integration test command>`は`test:e2e`または`test:integration*`のいずれかであり、`<file_name>`は`integration-tests/`ディレクトリ内の`.test.js`ファイルのいずれかです。たとえば、次のコマンドは`list_directory.test.js`と`write_file.test.js`を実行します。

```bash
npm run test:e2e list_directory write_file
```

### 名前による単一テストの実行

名前で単一のテストを実行するには、`--test-name-pattern`フラグを使用します。

```bash
npm run test:e2e -- --test-name-pattern "reads a file"
```

### すべてのテストの実行

統合テストのスイート全体を実行するには、次のコマンドを使用します。

```bash
npm run test:integration:all
```

### サンドボックスマトリックス

`all`コマンドは、`no sandboxing`、`docker`、`podman`のテストを実行します。
各個別のタイプは、次のコマンドを使用して実行できます。

```bash
npm run test:integration:sandbox:none
```

```bash
npm run test:integration:sandbox:docker
```

```bash
npm run test:integration:sandbox:podman
```

## 診断

統合テストランナーは、テストの失敗を追跡するのに役立ついくつかの診断オプションを提供します。

### テスト出力の保持

検査のために、テスト実行中に作成された一時ファイルを保持できます。これは、ファイルシステム操作の問題をデバッグするのに役立ちます。

テスト出力を保持するには、`--keep-output`フラグを使用するか、`KEEP_OUTPUT`環境変数を`true`に設定します。

```bash
# フラグの使用
npm run test:integration:sandbox:none -- --keep-output

# 環境変数の使用
KEEP_OUTPUT=true npm run test:integration:sandbox:none
```

出力が保持されると、テストランナーはテスト実行のための一意のディレクトリへのパスを出力します。

### 詳細出力

より詳細なデバッグのために、`--verbose`フラグは`gemini`コマンドからのリアルタイム出力をコンソールにストリーミングします。

```bash
npm run test:integration:sandbox:none -- --verbose
```

同じコマンドで`--verbose`と`--keep-output`を使用すると、出力はコンソールにストリーミングされ、テストの一時ディレクトリ内のログファイルにも保存されます。

詳細出力は、ログのソースを明確に識別するためにフォーマットされています。

```
--- TEST: <file-name-without-js>:<test-name> ---
... geminiコマンドからの出力 ...
--- END TEST: <file-name-without-js>:<test-name> ---
```

## リンティングとフォーマット

コードの品質と一貫性を確保するために、統合テストファイルはメインのビルドプロセスの一部としてリンティングされます。リンターと自動修正を手動で実行することもできます。

### リンターの実行

リンティングエラーをチェックするには、次のコマンドを実行します。

```bash
npm run lint
```

コマンドに`--fix`フラグを含めて、修正可能なリンティングエラーを自動的に修正できます。

```bash
npm run lint --fix
```

## ディレクトリ構造

統合テストは、`.integration-tests`ディレクトリ内にテスト実行ごとに一意のディレクトリを作成します。このディレクトリ内には、テストファイルごとにサブディレクトリが作成され、その中には個々のテストケースごとにサブディレクトリが作成されます。

この構造により、特定のテスト実行、ファイル、またはケースのアーティファクトを簡単に見つけることができます。

```
.integration-tests/
└── <run-id>/
    └── <test-file-name>.test.js/
        └── <test-case-name>/
            ├── output.log
            └── ...その他のテストアーティファクト...
```

## 継続的インテグレーション

統合テストが常に実行されるように、GitHub Actionsワークフローが`.github/workflows/e2e.yml`で定義されています。このワークフローは、すべてのプルリクエストと`main`ブランチへのプッシュで統合テストを自動的に実行します。

ワークフローは、Gemini CLIがそれぞれでテストされるように、さまざまなサンドボックス環境でテストを実行します。

- `sandbox:none`: サンドボックスなしでテストを実行します。
- `sandbox:docker`: Dockerコンテナでテストを実行します。
- `sandbox:podman`: Podmanコンテナでテストを実行します。 