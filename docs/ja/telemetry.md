# Gemini CLI 可観測性ガイド

テレメトリは、Gemini CLIのパフォーマンス、健全性、および使用状況に関するデータを提供します。これを有効にすることで、トレース、メトリクス、構造化ログを通じて、操作の監視、問題のデバッグ、ツールの使用の最適化を行うことができます。

Gemini CLIのテレメトリシステムは、**[OpenTelemetry] (OTEL)** 標準に基づいて構築されており、互換性のあるバックエンドにデータを送信できます。

[OpenTelemetry]: https://opentelemetry.io/

## テレメトリの有効化

テレメトリは複数の方法で有効にできます。設定は主に[`~/.gemini/settings.json` ファイル](./cli/configuration.md)と環境変数を介して管理されますが、CLIフラグは特定のセッションに対してこれらの設定を上書きできます。

### 優先順位

以下に、テレメトリ設定を適用するための優先順位を示します。上位にリストされている項目ほど優先順位が高くなります。

1.  **CLIフラグ (`gemini` コマンド用):**

    - `--telemetry` / `--no-telemetry`: `telemetry.enabled`を上書きします。
    - `--telemetry-target <local|gcp>`: `telemetry.target`を上書きします。
    - `--telemetry-otlp-endpoint <URL>`: `telemetry.otlpEndpoint`を上書きします。
    - `--telemetry-log-prompts` / `--no-telemetry-log-prompts`: `telemetry.logPrompts`を上書きします。

2.  **環境変数:**

    - `OTEL_EXPORTER_OTLP_ENDPOINT`: `telemetry.otlpEndpoint`を上書きします。

3.  **ワークスペース設定ファイル (`.gemini/settings.json`):** このプロジェクト固有のファイルの`telemetry`オブジェクトからの値。

4.  **ユーザー設定ファイル (`~/.gemini/settings.json`):** このグローバルユーザーファイルの`telemetry`オブジェクトからの値。

5.  **デフォルト:** 上記のいずれによっても設定されていない場合に適用されます。
    - `telemetry.enabled`: `false`
    - `telemetry.target`: `local`
    - `telemetry.otlpEndpoint`: `http://localhost:4317`
    - `telemetry.logPrompts`: `true`

**`npm run telemetry -- --target=<gcp|local>` スクリプトの場合:**
このスクリプトへの`--target`引数は、そのスクリプトの期間と目的のために`telemetry.target`を_のみ_上書きします (つまり、どのコレクターを開始するかを選択します)。`settings.json`を永続的に変更するものではありません。スクリプトは、デフォルトとして使用する`telemetry.target`を最初に`settings.json`で探します。

### 設定例

次のコードをワークスペース (`.gemini/settings.json`) またはユーザー (`~/.gemini/settings.json`) の設定に追加して、テレメトリを有効にし、出力をGoogle Cloudに送信できます。

```json
{
  "telemetry": {
    "enabled": true,
    "target": "gcp"
  },
  "sandbox": false
}
```

## OTELコレクターの実行

OTELコレクターは、テレメトリデータを受信、処理、エクスポートするサービスです。
CLIは、OTLP/gRPCプロトコルを使用してデータを送信します。

OTELエクスポーターの標準設定の詳細については、[ドキュメント][otel-config-docs]を参照してください。

[otel-config-docs]: https://opentelemetry.io/docs/languages/sdk-configuration/otlp-exporter/

### ローカル

`npm run telemetry -- --target=local` コマンドを使用して、`.gemini/settings.json` ファイルに必要な設定を構成するなど、ローカルテレメトリパイプラインのセットアッププロセスを自動化します。基になるスクリプトは、`otelcol-contrib` (OpenTelemetry Collector) と`jaeger` (トレースを表示するためのJaeger UI) をインストールします。これを使用するには:

1.  **コマンドの実行**:
    リポジトリのルートからコマンドを実行します。

    ```bash
    npm run telemetry -- --target=local
    ```

    スクリプトは次のことを行います。

    - 必要に応じてJaegerとOTELをダウンロードします。
    - ローカルのJaegerインスタンスを開始します。
    - Gemini CLIからデータを受信するように設定されたOTELコレクターを開始します。
    - ワークスペース設定でテレメトリを自動的に有効にします。
    - 終了時に、テレメトリを無効にします。

2.  **トレースの表示**:
    Webブラウザーを開き、**http://localhost:16686** に移動してJaeger UIにアクセスします。ここで、Gemini CLI操作の詳細なトレースを検査できます。

3.  **ログとメトリクスの検査**:
    スクリプトは、OTELコレクターの出力 (ログとメトリクスを含む) を`~/.gemini/tmp/<projectHash>/otel/collector.log`にリダイレクトします。スクリプトは、ローカルでテレメトリデータ (トレース、メトリクス、ログ) を表示するためのリンクとコマンドを提供します。

4.  **サービスの停止**:
    スクリプトが実行されているターミナルで`Ctrl+C`を押して、OTEL CollectorおよびJaegerサービスを停止します。

### Google Cloud

`npm run telemetry -- --target=gcp` コマンドを使用して、`.gemini/settings.json` ファイルに必要な設定を構成するなど、Google Cloudプロジェクトにデータを転送するローカルのOpenTelemetryコレクターのセットアップを自動化します。基になるスクリプトは`otelcol-contrib`をインストールします。これを使用するには:

1.  **前提条件**:

    - Google CloudプロジェクトIDを持っていること。
    - OTELコレクターで利用できるように、`GOOGLE_CLOUD_PROJECT`環境変数をエクスポートします。
      ```bash
      export OTLP_GOOGLE_CLOUD_PROJECT="your-project-id"
      ```
    - Google Cloudで認証します (例: `gcloud auth application-default login`を実行するか、`GOOGLE_APPLICATION_CREDENTIALS`が設定されていることを確認します)。
    - Google Cloudアカウント/サービスアカウントに必要なIAMロールがあることを確認します: 「Cloud Traceエージェント」、「モニタリング指標の書き込み」、「ログ書き込み」。

2.  **コマンドの実行**:
    リポジトリのルートからコマンドを実行します。

    ```bash
    npm run telemetry -- --target=gcp
    ```

    スクリプトは次のことを行います。

    - 必要に応じて`otelcol-contrib`バイナリをダウンロードします。
    - Gemini CLIからデータを受信し、指定されたGoogle Cloudプロジェクトにエクスポートするように設定されたOTELコレクターを開始します。
    - ワークスペース設定 (`.gemini/settings.json`) でテレメトリを自動的に有効にし、サンドボックスモードを無効にします。
    - Google Cloudコンソールでトレース、メトリクス、ログを表示するための直接リンクを提供します。
    - 終了時 (Ctrl+C)、元のテレメトリとサンドボックスの設定を復元しようとします。

3.  **Gemini CLIの実行:**
    別のターミナルで、Gemini CLIコマンドを実行します。これにより、コレクターがキャプチャするテレメトリデータが生成されます。

4.  **Google Cloudでテレメトリを表示**:
    スクリプトによって提供されたリンクを使用してGoogle Cloudコンソールに移動し、トレース、メトリクス、ログを表示します。

5.  **ローカルコレクターのログを検査**:
    スクリプトは、ローカルOTELコレクターの出力を`~/.gemini/tmp/<projectHash>/otel/collector-gcp.log`にリダイレクトします。スクリプトは、ローカルでコレクターのログを表示するためのリンクとコマンドを提供します。

6.  **サービスの停止**:
    スクリプトが実行されているターミナルで`Ctrl+C`を押して、OTEL Collectorを停止します。

## ログとメトリクスのリファレンス

次のセクションでは、Gemini CLI用に生成されたログとメトリクスの構造について説明します。

- すべてのログとメトリクスの共通属性として`sessionId`が含まれています。

### ログ

ログは、特定のイベントのタイムスタンプ付きレコードです。Gemini CLIでは、次のイベントがログに記録されます。

- `gemini_cli.config`: このイベントは、CLIの設定で起動時に1回発生します。

  - **属性**:
    - `model` (string)
    - `embedding_model` (string)
    - `sandbox_enabled` (boolean)
    - `core_tools_enabled` (string)
    - `approval_mode` (string)
    - `api_key_enabled` (boolean)
    - `vertex_ai_enabled` (boolean)
    - `code_assist_enabled` (boolean)
    - `log_prompts_enabled` (boolean)
    - `file_filtering_respect_git_ignore` (boolean)
    - `debug_mode` (boolean)
    - `mcp_servers` (string)

- `gemini_cli.user_prompt`: このイベントは、ユーザーがプロンプトを送信したときに発生します。

  - **属性**:
    - `prompt_length`
    - `prompt` (この属性は、`log_prompts_enabled`が`false`に設定されている場合は除外されます)

- `gemini_cli.tool_call`: このイベントは、各関数呼び出しに対して発生します。

  - **属性**:
    - `function_name`
    - `function_args`
    - `duration_ms`
    - `success` (boolean)
    - `decision` (string: "accept"、"reject"、または"modify"、該当する場合)
    - `error` (該当する場合)
    - `error_type` (該当する場合)

- `gemini_cli.api_request`: このイベントは、Gemini APIにリクエストを行うときに発生します。

  - **属性**:
    - `model`
    - `request_text` (該当する場合)

- `gemini_cli.api_error`: このイベントは、APIリクエストが失敗した場合に発生します。

  - **属性**:
    - `model`
    - `error`
    - `error_type`
    - `status_code`
    - `duration_ms`

- `gemini_cli.api_response`: このイベントは、Gemini APIから応答を受信したときに発生します。

  - **属性**:
    - `model`
    - `status_code`
    - `duration_ms`
    - `error` (オプション)
    - `input_token_count`
    - `output_token_count`
    - `cached_content_token_count`
    - `thoughts_token_count`
    - `tool_token_count`
    - `response_text` (該当する場合)

### メトリクス

メトリクスは、時間の経過に伴う動作の数値測定値です。Gemini CLIでは、次のメトリクスが収集されます。

- `gemini_cli.session.count` (カウンター、Int): CLIの起動ごとに1回インクリメントされます。

- `gemini_cli.tool.call.count` (カウンター、Int): ツール呼び出しをカウントします。

  - **属性**:
    - `function_name`
    - `success` (boolean)
    - `decision` (string: "accept"、"reject"、または"modify"、該当する場合)

- `gemini_cli.tool.call.latency` (ヒストグラム、ms): ツール呼び出しの待機時間を測定します。

  - **属性**:
    - `function_name`
    - `decision` (string: "accept"、"reject"、または"modify"、該当する場合)

- `gemini_cli.api.request.count` (カウンター、Int): すべてのAPIリクエストをカウントします。

  - **属性**:
    - `model`
    - `status_code`
    - `error_type` (該当する場合)

- `gemini_cli.api.request.latency` (ヒストグラム、ms): APIリクエストの待機時間を測定します。

  - **属性**:
    - `model`

- `gemini_cli.token.usage` (カウンター、Int): 使用されたトークンの数をカウントします。

  - **属性**:
    - `model`
    - `type` (string: "input"、"output"、"thought"、"cache"、または"tool")

- `gemini_cli.file.operation.count` (カウンター、Int): ファイル操作をカウントします。

  - **属性**:
    - `operation` (string: "create"、"read"、"update"): ファイル操作の種類。
    - `lines` (Int、該当する場合): ファイル内の行数。
    - `mimetype` (string、該当する場合): ファイルのMIMEタイプ。
    - `extension` (string、該当する場合): ファイルの拡張子。 