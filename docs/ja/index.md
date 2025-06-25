# Gemini CLIドキュメントへようこそ

このドキュメントでは、Gemini CLIのインストール、使用、開発に関する包括的なガイドを提供します。このツールを使用すると、コマンドラインインターフェースを介してGeminiモデルと対話できます。

## 概要

Gemini CLIは、インタラクティブなRead-Eval-Print Loop（REPL）環境で、Geminiモデルの機能をターミナルにもたらします。Gemini CLIは、ローカルサーバー（`packages/core`）と通信するクライアント側アプリケーション（`packages/cli`）で構成されています。ローカルサーバーは、Gemini APIとそのAIモデルへのリクエストを管理します。Gemini CLIには、ファイルシステム操作の実行、シェルの実行、Webフェッチなどのタスク用のさまざまなツールも含まれており、これらは`packages/core`によって管理されます。

## ドキュメントのナビゲート

このドキュメントは、次のセクションで構成されています。

- **[実行とデプロイ](./deployment.md):** Gemini CLIの実行に関する情報。
- **[アーキテクチャ概要](./architecture.md):** コンポーネントとその相互作用方法など、Gemini CLIの高度な設計を理解します。
- **CLIの使用法:** `packages/cli`のドキュメント。
  - **[CLIはじめに](./cli/index.md):** コマンドラインインターフェースの概要。
  - **[コマンド](./cli/commands.md):** 利用可能なCLIコマンドの説明。
  - **[設定](./cli/configuration.md):** CLIの設定に関する情報。
  - **[チェックポイント](./checkpointing.md):** チェックポイント機能のドキュメント。
  - **[拡張機能](./extension.md):** 新しい機能でCLIを拡張する方法。
  - **[テレメトリ](./telemetry.md):** CLIのテレメトリの概要。
- **コアの詳細:** `packages/core`のドキュメント。
  - **[コアはじめに](./core/index.md):** コアコンポーネントの概要。
  - **[ツールAPI](./core/tools-api.md):** コアがツールを管理および公開する方法に関する情報。
- **ツール:**
  - **[ツール概要](./tools/index.md):** 利用可能なツールの概要。
  - **[ファイルシステムツール](./tools/file-system.md):** `read_file`および`write_file`ツールのドキュメント。
  - **[複数ファイル読み取りツール](./tools/multi-file.md):** `read_many_files`ツールのドキュメント。
  - **[シェルツール](./tools/shell.md):** `run_shell_command`ツールのドキュメント。
  - **[Webフェッチツール](./tools/web-fetch.md):** `web_fetch`ツールのドキュメント。
  - **[Web検索ツール](./tools/web-search.md):** `google_web_search`ツールのドキュメント。
  - **[メモリツール](./tools/memory.md):** `save_memory`ツールのドキュメント。
- **[貢献と開発ガイド](../CONTRIBUTING.md):** セットアップ、ビルド、テスト、コーディング規約など、貢献者と開発者向けの情報。
- **[トラブルシューティングガイド](./troubleshooting.md):** 一般的な問題とFAQの解決策を見つけます。

このドキュメントがGemini CLIを最大限に活用するのに役立つことを願っています！ 