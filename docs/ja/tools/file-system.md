# Gemini CLIファイルシステムツール

Gemini CLIは、ローカルファイルシステムと対話するための包括的なツールスイートを提供します。これらのツールを使用すると、Geminiモデルは、ユーザーの制御下で、通常は機密性の高い操作の確認を経て、ファイルの読み取り、書き込み、一覧表示、検索、および変更を行うことができます。

**注:** すべてのファイルシステムツールは、セキュリティのために`rootDirectory`（通常はCLIを起動した現在の作業ディレクトリ）内で動作します。これらのツールに提供するパスは、通常、絶対パスであるか、このルートディレクトリからの相対パスとして解決されることが想定されています。

## 1. `list_directory` (ReadFolder)

`list_directory`は、指定されたディレクトリパス内のファイルとサブディレクトリの名前を直接一覧表示します。オプションで、提供されたglobパターンに一致するエントリを無視できます。

- **ツール名:** `list_directory`
- **表示名:** ReadFolder
- **ファイル:** `ls.ts`
- **パラメーター:**
  - `path`（文字列、必須）：一覧表示するディレクトリへの絶対パス。
  - `ignore`（文字列の配列、オプション）：リストから除外するglobパターンのリスト（例：`["*.log", ".git"]`）。
  - `respect_git_ignore`（ブール値、オプション）：ファイルを一覧表示するときに`.gitignore`パターンを尊重するかどうか。デフォルトは`true`です。
- **動作:**
  - ファイルとディレクトリ名のリストを返します。
  - 各エントリがディレクトリであるかどうかを示します。
  - ディレクトリを最初に、次にアルファベット順にエントリを並べ替えます。
- **出力 (`llmContent`):** `Directory listing for /path/to/your/folder:\n[DIR] subfolder1\nfile1.txt\nfile2.png`のような文字列
- **確認:** いいえ。
- **ツール名:** `list_directory`
- **表示名:** ReadFolder
- **ファイル:** `ls.ts`
- **パラメーター:**
  - `path`（文字列、必須）：一覧表示するディレクトリへの絶対パス。
  - `ignore`（文字列の配列、オプション）：リストから除外するglobパターンのリスト（例：`["*.log", ".git"]`）。
  - `respect_git_ignore`（ブール値、オプション）：ファイルを一覧表示するときに`.gitignore`パターンを尊重するかどうか。デフォルトは`true`です。
- **動作:**
  - ファイルとディレクトリ名のリストを返します。
  - 各エントリがディレクトリであるかどうかを示します。
  - ディレクトリを最初に、次にアルファベット順にエントリを並べ替えます。
- **出力 (`llmContent`):** `Directory listing for /path/to/your/folder:\n[DIR] subfolder1\nfile1.txt\nfile2.png`のような文字列
- **確認:** いいえ。

## 2. `read_file` (ReadFile)

`read_file`は、指定されたファイルの内容を読み取って返します。このツールは、テキスト、画像（PNG、JPG、GIF、WEBP、SVG、BMP）、およびPDFファイルを処理します。テキストファイルの場合、特定の行範囲を読み取ることができます。他のバイナリファイルタイプは通常スキップされます。

- **ツール名:** `read_file`
- **表示名:** ReadFile
- **ファイル:** `read-file.ts`
- **パラメーター:**
  - `path`（文字列、必須）：読み取るファイルへの絶対パス。
  - `offset`（数値、オプション）：テキストファイルの場合、読み取りを開始する0ベースの行番号。`limit`を設定する必要があります。
  - `limit`（数値、オプション）：テキストファイルの場合、読み取る最大行数。省略した場合、デフォルトの最大値（例：2000行）または可能な場合はファイル全体を読み取ります。
- **動作:**
  - テキストファイルの場合：コンテンツを返します。`offset`と`limit`が使用されている場合、その行のスライスのみを返します。行制限または行長制限によりコンテンツが切り捨てられたかどうかを示します。
  - 画像およびPDFファイルの場合：モデル消費に適したbase64エンコードデータ構造としてファイルコンテンツを返します。
  - 他のバイナリファイルの場合：それらを識別してスキップしようとし、汎用バイナリファイルであることを示すメッセージを返します。
- **出力:** (`llmContent`):
  - テキストファイルの場合：コンテンツ。切り捨てメッセージが前に付加される場合があります（例：`[File content truncated: showing lines 1-100 of 500 total lines...]\nActual file content...`）。
  - 画像/ PDFファイルの場合：`mimeType`とbase64 `data`を含む`inlineData`を含むオブジェクト（例：`{ inlineData: { mimeType: 'image/png', data: 'base64encodedstring' } }`）。
  - 他のバイナリファイルの場合：`/path/to/data.bin`のようなバイナリファイルの内容を表示できないというメッセージ。
- **確認:** いいえ。
- **ツール名:** `read_file`
- **表示名:** ReadFile
- **ファイル:** `read-file.ts`
- **パラメーター:**
  - `path`（文字列、必須）：読み取るファイルへの絶対パス。
  - `offset`（数値、オプション）：テキストファイルの場合、読み取りを開始する0ベースの行番号。`limit`を設定する必要があります。
  - `limit`（数値、オプション）：テキストファイルの場合、読み取る最大行数。省略した場合、デフォルトの最大値（例：2000行）または可能な場合はファイル全体を読み取ります。
- **動作:**
  - テキストファイルの場合：コンテンツを返します。`offset`と`limit`が使用されている場合、その行のスライスのみを返します。行制限または行長制限によりコンテンツが切り捨てられたかどうかを示します。
  - 画像およびPDFファイルの場合：モデル消費に適したbase64エンコードデータ構造としてファイルコンテンツを返します。
  - 他のバイナリファイルの場合：それらを識別してスキップしようとし、汎用バイナリファイルであることを示すメッセージを返します。
- **出力:** (`llmContent`):
  - テキストファイルの場合：コンテンツ。切り捨てメッセージが前に付加される場合があります（例：`[File content truncated: showing lines 1-100 of 500 total lines...]\nActual file content...`）。
  - 画像/ PDFファイルの場合：`mimeType`とbase64 `data`を含む`inlineData`を含むオブジェクト（例：`{ inlineData: { mimeType: 'image/png', data: 'base64encodedstring' } }`）。
  - 他のバイナリファイルの場合：`/path/to/data.bin`のようなバイナリファイルの内容を表示できないというメッセージ。
- **確認:** いいえ。

## 3. `write_file` (WriteFile)

`write_file`は、指定されたファイルにコンテンツを書き込みます。ファイルが存在する場合、上書きされます。ファイルが存在しない場合、それ（および必要な親ディレクトリ）が作成されます。

- **ツール名:** `write_file`
- **表示名:** WriteFile
- **ファイル:** `write-file.ts`
- **パラメーター:**
  - `file_path`（文字列、必須）：書き込むファイルへの絶対パス。
  - `content`（文字列、必須）：ファイルに書き込むコンテンツ。
- **動作:**
  - 提供された`content`を`file_path`に書き込みます。
  - 親ディレクトリが存在しない場合は作成します。
- **出力 (`llmContent`):** 成功メッセージ。例：`Successfully overwrote file: /path/to/your/file.txt`または`Successfully created and wrote to new file: /path/to/new/file.txt`。
- **確認:** はい。変更の差分を表示し、書き込む前にユーザーの承認を求めます。
- **ツール名:** `write_file`
- **表示名:** WriteFile
- **ファイル:** `write-file.ts`
- **パラメーター:**
  - `file_path`（文字列、必須）：書き込むファイルへの絶対パス。
  - `content`（文字列、必須）：ファイルに書き込むコンテンツ。
- **動作:**
  - 提供された`content`を`file_path`に書き込みます。
  - 親ディレクトリが存在しない場合は作成します。
- **出力 (`llmContent`):** 成功メッセージ。例：`Successfully overwrote file: /path/to/your/file.txt`または`Successfully created and wrote to new file: /path/to/new/file.txt`。
- **確認:** はい。変更の差分を表示し、書き込む前にユーザーの承認を求めます。

## 4. `glob` (FindFiles)

`glob`は、特定のglobパターン（例：`src/**/*.ts`、`*.md`）に一致するファイルを検索し、変更時刻（新しい順）で並べ替えられた絶対パスを返します。

- **ツール名:** `glob`
- **表示名:** FindFiles
- **ファイル:** `glob.ts`
- **パラメーター:**
  - `pattern`（文字列、必須）：照合するglobパターン（例：`"*.py"`、`"src/**/*.js"`）。
  - `path`（文字列、オプション）：検索するディレクトリへの絶対パス。省略した場合、ツールのルートディレクトリを検索します。
  - `case_sensitive`（ブール値、オプション）：検索で大文字と小文字を区別するかどうか。デフォルトは`false`です。
  - `respect_git_ignore`（ブール値、オプション）：ファイルを検索するときに.gitignoreパターンを尊重するかどうか。デフォルトは`true`です。
- **動作:**
  - 指定されたディレクトリ内でglobパターンに一致するファイルを検索します。
  - 最近変更されたファイルが最初にくるように並べ替えられた絶対パスのリストを返します。
  - デフォルトで`node_modules`や`.git`などの一般的な迷惑なディレクトリを無視します。
- **出力 (`llmContent`):** `Found 5 file(s) matching "*.ts" within src, sorted by modification time (newest first):\nsrc/file1.ts\nsrc/subdir/file2.ts...`のようなメッセージ
- **確認:** いいえ。
- **ツール名:** `glob`
- **表示名:** FindFiles
- **ファイル:** `glob.ts`
- **パラメーター:**
  - `pattern`（文字列、必須）：照合するglobパターン（例：`"*.py"`、`"src/**/*.js"`）。
  - `path`（文字列、オプション）：検索するディレクトリへの絶対パス。省略した場合、ツールのルートディレクトリを検索します。
  - `case_sensitive`（ブール値、オプション）：検索で大文字と小文字を区別するかどうか。デフォルトは`false`です。
  - `respect_git_ignore`（ブール値、オプション）：ファイルを検索するときに.gitignoreパターンを尊重するかどうか。デフォルトは`true`です。
- **動作:**
  - 指定されたディレクトリ内でglobパターンに一致するファイルを検索します。
  - 最近変更されたファイルが最初にくるように並べ替えられた絶対パスのリストを返します。
  - デフォルトで`node_modules`や`.git`などの一般的な迷惑なディレクトリを無視します。
- **出力 (`llmContent`):** `Found 5 file(s) matching "*.ts" within src, sorted by modification time (newest first):\nsrc/file1.ts\nsrc/subdir/file2.ts...`のようなメッセージ
- **確認:** いいえ。

## 5. `search_file_content` (SearchText)

`search_file_content`は、指定されたディレクトリ内のファイルの内容から正規表現パターンを検索します。globパターンでファイルをフィルタリングできます。一致する行を、ファイルパスと行番号とともに返します。

- **ツール名:** `search_file_content`
- **表示名:** SearchText
- **ファイル:** `grep.ts`
- **パラメーター:**
  - `pattern`（文字列、必須）：検索する正規表現（regex）（例：`"function\s+myFunction"`）。
  - `path`（文字列、オプション）：検索するディレクトリへの絶対パス。デフォルトは現在の作業ディレクトリです。
  - `include`（文字列、オプション）：検索対象のファイルをフィルタリングするためのglobパターン（例：`"*.js"`、`"src/**/*.{ts,tsx}"`）。省略した場合、ほとんどのファイル（一般的な無視を尊重）を検索します。
- **動作:**
  - 高速化のためにGitリポジトリで利用可能な場合は`git grep`を使用し、それ以外の場合はシステムの`grep`またはJavaScriptベースの検索にフォールバックします。
  - 一致する行のリストを返します。各行には、ファイルパス（検索ディレクトリからの相対パス）と行番号が前に付加されます。
- **出力 (`llmContent`):** 一致のフォーマット済み文字列。例：
- **ツール名:** `search_file_content`
- **表示名:** SearchText
- **ファイル:** `grep.ts`
- **パラメーター:**
  - `pattern`（文字列、必須）：検索する正規表現（regex）（例：`"function\s+myFunction"`）。
  - `path`（文字列、オプション）：検索するディレクトリへの絶対パス。デフォルトは現在の作業ディレクトリです。
  - `include`（文字列、オプション）：検索対象のファイルをフィルタリングするためのglobパターン（例：`"*.js"`、`"src/**/*.{ts,tsx}"`）。省略した場合、ほとんどのファイル（一般的な無視を尊重）を検索します。
- **動作:**
  - 高速化のためにGitリポジトリで利用可能な場合は`git grep`を使用し、それ以外の場合はシステムの`grep`またはJavaScriptベースの検索にフォールバックします。
  - 一致する行のリストを返します。各行には、ファイルパス（検索ディレクトリからの相対パス）と行番号が前に付加されます。
- **出力 (`llmContent`):** 一致のフォーマット済み文字列。例：
  ```
  Found 3 match(es) for pattern "myFunction" in path "." (filter: "*.ts"):
  ---
  File: src/utils.ts
  L15: export function myFunction() {
  L22:   myFunction.call();
  ---
  File: src/index.ts
  L5: import { myFunction } from './utils';
  ---
  ```
- **確認:** いいえ。
- **確認:** いいえ。

## 6. `replace` (Edit)

`replace`は、ファイル内のテキストを置換します。デフォルトでは、1つの出現箇所を置換しますが、`expected_replacements`が指定されている場合は複数の出現箇所を置換できます。このツールは、正確で的を絞った変更用に設計されており、正しい場所を変更するために`old_string`の周囲にかなりのコンテキストが必要です。

- **ツール名:** `replace`
- **表示名:** Edit
- **ファイル:** `edit.ts`
- **パラメーター:**

  - `file_path`（文字列、必須）：変更するファイルへの絶対パス。
  - `old_string`（文字列、必須）：置換する正確なリテラルテキスト。

    **重要:** この文字列は、変更する単一のインスタンスを一意に識別する必要があります。ターゲットテキストの*前*と*後*に少なくとも3行のコンテキストを含め、空白とインデントを正確に一致させる必要があります。`old_string`が空の場合、ツールは`file_path`に`new_string`をコンテンツとして新しいファイルを作成しようとします。

  - `new_string`（文字列、必須）：`old_string`を置換する正確なリテラルテキスト。
  - `expected_replacements`（数値、オプション）：置換する出現箇所の数。デフォルトは`1`です。

- **動作:**
  - `old_string`が空で`file_path`が存在しない場合、`new_string`をコンテンツとして新しいファイルを作成します。
  - `old_string`が指定されている場合、`file_path`を読み取り、`old_string`の出現箇所を1つだけ見つけようとします。
  - 1つの出現箇所が見つかった場合、それを`new_string`で置換します。
  - **信頼性の向上（多段階編集修正）:** 特にモデルが提供する`old_string`が完全には正確でない場合に備えて、編集の成功率を大幅に向上させるために、ツールは多段階編集修正メカニズムを組み込んでいます。
  - 初期の`old_string`が見つからない場合や複数の場所に一致する場合、ツールはGeminiモデルを活用して`old_string`（および場合によっては`new_string`）を繰り返し改良できます。
  - この自己修正プロセスは、モデルが変更しようとした一意のセグメントを特定しようとするため、わずかに不完全な初期コンテキストでも`replace`操作がより堅牢になります。
- **失敗条件:** 修正メカニズムにもかかわらず、次の場合にツールは失敗します。
  - `file_path`が絶対パスでないか、ルートディレクトリの外にある。
  - `old_string`は空ではありませんが、`file_path`は存在しません。
  - `old_string`は空ですが、`file_path`はすでに存在します。
  - `old_string`は、修正しようとしてもファイル内で見つかりません。
  - `old_string`が複数回見つかり、自己修正メカニズムがそれを単一の明確な一致に解決できない。
- **出力 (`llmContent`):**
  - 成功時：`Successfully modified file: /path/to/file.txt (1 replacements).`または`Created new file: /path/to/new_file.txt with provided content.`
  - 失敗時：理由を説明するエラーメッセージ（例：`Failed to edit, 0 occurrences found...`、`Failed to edit, expected 1 occurrences but found 2...`）。
- **確認:** はい。提案された変更の差分を表示し、ファイルに書き込む前にユーザーの承認を求めます。

  - `new_string`（文字列、必須）：`old_string`を置換する正確なリテラルテキスト。
  - `expected_replacements`（数値、オプション）：置換する出現箇所の数。デフォルトは`1`です。

- **動作:**
  - `old_string`が空で`file_path`が存在しない場合、`new_string`をコンテンツとして新しいファイルを作成します。
  - `old_string`が指定されている場合、`file_path`を読み取り、`old_string`の出現箇所を1つだけ見つけようとします。
  - 1つの出現箇所が見つかった場合、それを`new_string`で置換します。
  - **信頼性の向上（多段階編集修正）:** 特にモデルが提供する`old_string`が完全には正確でない場合に備えて、編集の成功率を大幅に向上させるために、ツールは多段階編集修正メカニズムを組み込んでいます。
    - 初期の`old_string`が見つからない場合や複数の場所に一致する場合、ツールはGeminiモデルを活用して`old_string`（および場合によっては`new_string`）を繰り返し改良できます。
    - この自己修正プロセスは、モデルが変更しようとした一意のセグメントを特定しようとするため、わずかに不完全な初期コンテキストでも`replace`操作がより堅牢になります。
- **失敗条件:** 修正メカニズムにもかかわらず、次の場合にツールは失敗します。
  - `file_path`が絶対パスでないか、ルートディレクトリの外にある。
  - `old_string`は空ではありませんが、`file_path`は存在しません。
  - `old_string`は空ですが、`file_path`はすでに存在します。
  - `old_string`は、修正しようとしてもファイル内で見つかりません。
  - `old_string`が複数回見つかり、自己修正メカニズムがそれを単一の明確な一致に解決できない。
- **出力 (`llmContent`):**
  - 成功時：`Successfully modified file: /path/to/file.txt (1 replacements).`または`Created new file: /path/to/new_file.txt with provided content.`
  - 失敗時：理由を説明するエラーメッセージ（例：`Failed to edit, 0 occurrences found...`、`Failed to edit, expected 1 occurrences but found 2...`）。
- **確認:** はい。提案された変更の差分を表示し、ファイルに書き込む前にユーザーの承認を求めます。

これらのファイルシステムツールは、Gemini CLIがローカルプロジェクトのコンテキストを理解し、対話するための基盤を提供します。 