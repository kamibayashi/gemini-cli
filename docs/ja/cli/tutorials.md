# チュートリアル

このページには、Gemini CLIを操作するためのチュートリアルが含まれています。

## モデルコンテキストプロトコル（MCP）サーバーの設定

> [!CAUTION]
> サードパーティのMCPサーバーを使用する前に、そのソースを信頼し、提供されるツールを理解していることを確認してください。サードパーティサーバーの使用は、ご自身の責任で行ってください。

このチュートリアルでは、[GitHub MCPサーバー](https://github.com/github/github-mcp-server)を例として、MCPサーバーを設定する方法を説明します。GitHub MCPサーバーは、課題の作成やプルリクエストへのコメントなど、GitHubリポジトリと対話するためのツールを提供します。

### 前提条件

開始する前に、次のものがインストールされ、設定されていることを確認してください。

- **Docker:** [Docker]をインストールして実行します。
- **GitHubパーソナルアクセストークン（PAT）:** 必要なスコープを持つ新しい[クラシック]または[きめ細かい]PATを作成します。

[Docker]: https://www.docker.com/
[クラシック]: https://github.com/settings/tokens/new
[きめ細かい]: https://github.com/settings/personal-access-tokens/new

### ガイド

#### `settings.json`でMCPサーバーを構成する

プロジェクトのルートディレクトリで、[`.gemini/settings.json`ファイル](./configuration.md)を作成または開きます。ファイル内に、GitHub MCPサーバーの起動方法を指示する`mcpServers`構成ブロックを追加します。

```json
{
  "mcpServers": {
    "github": {
      "command": "docker",
      "args": [
        "run",
        "-i",
        "--rm",
        "-e",
        "GITHUB_PERSONAL_ACCESS_TOKEN",
        "ghcr.io/github/github-mcp-server"
      ],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "${GITHUB_PERSONAL_ACCESS_TOKEN}"
      }
    }
  }
}
```

#### GitHubトークンを設定する

> [!CAUTION]
> 個人リポジトリとプライベートリポジトリにアクセスできる広範囲のスコープを持つパーソナルアクセストークンを使用すると、プライベートリポジトリの情報がパブリックリポジトリに漏洩する可能性があります。パブリックリポジトリとプライベートリポジトリの両方へのアクセスを共有しない、きめ細かいアクセストークンを使用することをお勧めします。

環境変数を使用してGitHub PATを保存します。

```bash
GITHUB_PERSONAL_ACCESS_TOKEN="pat_YourActualGitHubTokenHere"
```

Gemini CLIは、`settings.json`ファイルで定義した`mcpServers`構成でこの値を使用します。

#### Gemini CLIを起動して接続を確認する

Gemini CLIを起動すると、構成が自動的に読み込まれ、バックグラウンドでGitHub MCPサーバーが起動します。その後、自然言語のプロンプトを使用して、Gemini CLIにGitHubアクションを実行するように依頼できます。例：

```bash
「foo/bar」リポジトリで自分に割り当てられているすべてのオープンな課題を取得し、優先順位を付けてください
``` 