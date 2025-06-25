## 認証設定

Gemini CLIでは、GoogleのAIサービスで認証する必要があります。初回起動時に、次のいずれかの認証方法を設定する必要があります。

1.  **Googleでログイン (Gemini Code Assist):**

    - このオプションを使用して、Gmail、Googleフォト、Googleドライブなどのサービスで個人用に（例：your-name@gmail.com）使用する標準の個人アカウントでログインします。
    - 初回起動時に、Gemini CLIは認証のためにWebページにリダイレクトします。認証されると、認証情報がローカルにキャッシュされるため、次回以降の実行ではWebログインをスキップできます。
    - Webログインは、Gemini CLIが実行されているマシンと通信できるブラウザで行う必要があることに注意してください。（具体的には、ブラウザはGemini CLIがリッスンしているlocalhostのURLにリダイレクトされます）。

2.  **Gemini APIキー:**

    - Google AI StudioからAPIキーを取得します: [https://aistudio.google.com/app/apikey](https://aistudio.google.com/app/apikey)
    - `GEMINI_API_KEY`環境変数を設定します。次の方法では、`YOUR_GEMINI_API_KEY`をGoogle AI Studioから取得したAPIキーに置き換えます。
      - 次のコマンドを使用して、現在のシェルセッションで環境変数を一時的に設定できます。
        ```bash
        export GEMINI_API_KEY="YOUR_GEMINI_API_KEY"
        ```
      - 繰り返し使用する場合は、環境変数を`.env`ファイル（プロジェクトディレクトリまたはユーザーのホームディレクトリにあります）またはシェルの設定ファイル（`~/.bashrc`、`~/.zshrc`、`~/.profile`など）に追加できます。たとえば、次のコマンドは環境変数を`~/.bashrc`ファイルに追加します。
        ```bash
        echo 'export GEMINI_API_KEY="YOUR_GEMINI_API_KEY"' >> ~/.bashrc
        source ~/.bashrc
        ```

3.  **Google Workspaceでログイン**

    - このオプションを使用して、**Google Workspaceアカウント**でログインします。これは、カスタムメールドメイン（例：your-name@your-company.com）、強化されたセキュリティ機能、管理者コントロールなどの生産性向上ツールスイートを提供する企業や組織向けの有料サービスです。これらのアカウントは、多くの場合、雇用主や学校によって管理されています。
      - Google Workspaceアカウントでは、最初に使用するGoogle CloudプロジェクトIDを設定し、[Gemini for Cloud APIを有効にし](https://cloud.google.com/gemini/docs/discover/set-up-gemini#enable-api)、[アクセス許可を設定する](https://cloud.google.com/gemini/docs/discover/set-up-gemini#grant-iam)必要があります。次のコマンドを使用して、現在のシェルセッションで環境変数を一時的に設定できます。
      ```bash
      export GOOGLE_CLOUD_PROJECT_ID="YOUR_PROJECT_ID"
      ```
      - 繰り返し使用する場合は、環境変数を`.env`ファイル（プロジェクトディレクトリまたはユーザーのホームディレクトリにあります）またはシェルの設定ファイル（`~/.bashrc`、`~/.zshrc`、`~/.profile`など）に追加できます。たとえば、次のコマンドは環境変数を`~/.bashrc`ファイルに追加します。
      ```bash
      echo 'export GOOGLE_CLOUD_PROJECT_ID="YOUR_PROJECT_ID"' >> ~/.bashrc
      source ~/.bashrc
      ```
    - 起動時に、Gemini CLIは認証のためにWebページにリダイレクトします。認証されると、認証情報がローカルにキャッシュされるため、次回以降の実行ではWebログインをスキップできます。
    - Webログインは、Gemini CLIが実行されているマシンと通信できるブラウザで行う必要があることに注意してください。（具体的には、ブラウザはGemini CLIがリッスンしているlocalhostのURLにリダイレクトされます）。

4.  **Vertex AI:**
    - エクスプレスモードを使用しない場合:
      - Google Cloudプロジェクトがあり、Vertex AI APIが有効になっていることを確認してください。
      - 次のコマンドを使用して、アプリケーションのデフォルト認証情報（ADC）を設定します。
        ```bash
        gcloud auth application-default login
        ```
        詳細については、[Google Cloudのアプリケーションデフォルト認証情報の設定](https://cloud.google.com/docs/authentication/provide-credentials-adc)を参照してください。
      - `GOOGLE_CLOUD_PROJECT`、`GOOGLE_CLOUD_LOCATION`、および`GOOGLE_GENAI_USE_VERTEXAI`環境変数を設定します。次の方法では、`YOUR_PROJECT_ID`と`YOUR_PROJECT_LOCATION`をプロジェクトに関連する値に置き換えます。
        - 次のコマンドを使用して、現在のシェルセッションでこれらの環境変数を一時的に設定できます。
          ```bash
          export GOOGLE_CLOUD_PROJECT="YOUR_PROJECT_ID"
          export GOOGLE_CLOUD_LOCATION="YOUR_PROJECT_LOCATION" # 例: us-central1
          export GOOGLE_GENAI_USE_VERTEXAI=true
          ```
        - 繰り返し使用する場合は、環境変数を`.env`ファイル（プロジェクトディレクトリまたはユーザーのホームディレクトリにあります）またはシェルの設定ファイル（`~/.bashrc`、`~/.zshrc`、`~/.profile`など）に追加できます。たとえば、次のコマンドは環境変数を`~/.bashrc`ファイルに追加します。
          ```bash
          echo 'export GOOGLE_CLOUD_PROJECT="YOUR_PROJECT_ID"' >> ~/.bashrc
          echo 'export GOOGLE_CLOUD_LOCATION="YOUR_PROJECT_LOCATION"' >> ~/.bashrc
          echo 'export GOOGLE_GENAI_USE_VERTEXAI=true' >> ~/.bashrc
          source ~/.bashrc
          ```
    - エクスプレスモードを使用する場合:
      - `GOOGLE_API_KEY`環境変数を設定します。次の方法では、`YOUR_GOOGLE_API_KEY`をエクスプレスモードで提供されるVertex AI APIキーに置き換えます。
        - 次のコマンドを使用して、現在のシェルセッションでこれらの環境変数を一時的に設定できます。
          ```bash
          export GOOGLE_API_KEY="YOUR_GOOGLE_API_KEY"
          export GOOGLE_GENAI_USE_VERTEXAI=true
          ```
        - 繰り返し使用する場合は、環境変数を`.env`ファイル（プロジェクトディレクトリまたはユーザーのホームディレクトリにあります）またはシェルの設定ファイル（`~/.bashrc`、`~/.zshrc`、`~/.profile`など）に追加できます。たとえば、次のコマンドは環境変数を`~/.bashrc`ファイルに追加します。
          ```bash
          echo 'export GOOGLE_API_KEY="YOUR_GOOGLE_API_KEY"' >> ~/.bashrc
          echo 'export GOOGLE_GENAI_USE_VERTEXAI=true' >> ~/.bashrc
          source ~/.bashrc
          ``` 