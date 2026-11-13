---
title: "演習 3 - Playwright MCP サーバーで機能をテストする"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 前のレッスン: Agent mode][previous-lesson] · [次のレッスン: カスタムエージェント →][next-lesson]

agent mode でフィルタリング機能を作成しました。プルリクエストを開く前に、ブラウザーで実際に動作することを確認します。自分でアプリを操作する代わりに、**Playwright MCP サーバー**を接続し、Copilot に実際のブラウザーを操作させて機能をテストします。その後、ブランチを公開して PR を作成します。

この演習では、次の内容を学習します。

- Model Context Protocol (MCP) と MCP サーバーが Copilot にツールを追加する仕組みを理解する。
- Playwright MCP サーバーをワークスペースに追加する。
- Copilot に Playwright MCP サーバーを使わせ、ブラウザーでフィルタリング機能を手動テストする。
- ブランチを公開し、フィルタリング作業のプルリクエストを作成する。

## Model Context Protocol (MCP) とは

Copilot は、エディターの外にアクセスできると agent mode の機能が大きく広がります。Model Context Protocol (MCP) はそのための標準で、エージェントが外部のツールやサービスと通信できるようにします。

![agent mode の内部構造と、MCP サーバーおよび VS Code 拡張機能が提供するツールを含むコンテキスト、大規模言語モデル (LLM)、ツール間の連携を示す図](../../../_images/ex1-mcp-diagram.png)

[Model Context Protocol (MCP)](https://github.blog/ai-and-ml/llms/what-the-heck-is-mcp-and-why-is-everyone-talking-about-it/) を使うと、AI エージェントは外部のツールやサービスと通信できます。MCP によって、AI エージェントはリソースを使って最新情報にアクセスしたり、ツールを使って開発者に代わって操作を実行したりできます。

ツールやリソースには、AI エージェントと外部ツールやサービスとの橋渡しをする MCP サーバーを介してアクセスします。MCP サーバーは、既存の API や npm パッケージなどのローカルツールとの通信を管理します。各 MCP サーバーは、AI エージェントが利用できる異なるツールとリソースを提供します。

よく使われる MCP サーバーの例を次に示します。

- **[GitHub MCP Server](https://github.com/github/github-mcp-server)**: GitHub リポジトリを管理するアプリケーション・プログラミング・インターフェイス (API) にアクセスできます。人工知能 (AI) エージェントは、新しいリポジトリの作成、既存リポジトリの更新、Issue やプルリクエストの管理などを実行できます。
- **[Playwright MCP Server](https://github.com/microsoft/playwright-mcp)**: Playwright によるブラウザー自動化機能を提供します。AI エージェントは、Web ページへの移動、フォームの入力、ボタンの選択などを実行できます。

さまざまなツールやリソースにアクセスできる MCP サーバーは、ほかにも多数あります。GitHub は、エコシステム内の MCP サーバーを見つけやすくし、貢献を促進するために [MCP レジストリ](https://github.com/mcp)をホストしています。

> [!CAUTION]
> セキュリティの観点から、MCP サーバーはプロジェクト内の他の依存関係と同様に扱ってください。利用前にソースコードを慎重に確認し、発行元を検証して、セキュリティへの影響を検討してください。信頼できる MCP サーバーのみを使い、機密性の高いリソースや操作へのアクセスを許可する際は注意してください。

## MCP の構成を確認する

`.vscode/mcp.json` ファイルで、この VS Code ワークスペースで利用する MCP サーバーを構成します。

1. Codespace で `.vscode/mcp.json` を開きます。
2. 次のように `github` サーバーがすでに構成されていることを確認します。

   ```json
   {
     "servers": {
       "github": {
         "type": "http",
         "url": "https://api.githubcopilot.com/mcp/"
       }
     }
   }
   ```

`github` の設定はプロジェクトテンプレートに含まれているため、前の演習で Copilot がバックログの Issue を読み取れました。この設定では[リモート GitHub MCP サーバー][remote-github-mcp-server]を使います。ローカルにインストールする必要はありません。VS Code は HTTP 経由で接続し、Copilot が初めてツールを使うときに GitHub で認証します。

## Playwright MCP サーバーを追加する

次に、2 つ目のサーバーを追加します。[Playwright MCP サーバー][playwright-mcp-server]を使うと Copilot がブラウザーを操作できるため、機能をテストできます。

1. `.vscode/mcp.json` で `github` と並べて `playwright` の項目を追加し、ファイルを次のようにします。

   ```json
   {
     "servers": {
       "github": {
         "type": "http",
         "url": "https://api.githubcopilot.com/mcp/"
       },
       "playwright": {
         "command": "npx",
         "args": ["@playwright/mcp@latest", "--headless"]
       }
     }
   }
   ```

2. ファイルを保存します。

リモートの `github` サーバーと異なり、`playwright` はローカルサーバーです。VS Code は `npx @playwright/mcp@latest` を実行してサーバーを起動します。`--headless` フラグを指定すると、ブラウザーを画面に表示せずに実行できます。Codespace には表示用デスクトップがないため、この設定が必要です。

> [!NOTE]
> Tailspin Toys プロジェクトはエンドツーエンドテストに Playwright を使うため、Playwright が必要とするブラウザーは通常すでにインストールされています。Copilot がブラウザーがないと報告した場合は、`npx playwright install chromium` を実行して再度試すよう依頼します。

## サーバーを起動して信頼する

VS Code は MCP サーバーを**必要に応じて起動**します。Copilot がサーバーのツールを初めて使うときに VS Code が起動し、サーバーを信頼するか確認します。

1. `.vscode/mcp.json` で `playwright` の設定の上に **Start** が表示されます。選択してサーバーを起動し、構成が正しいことを確認します。
2. サーバーを信頼するか確認されたら、構成を確認し、信頼して起動することを選択します。

手動で起動する手順は省略できます。その場合、次の手順で Copilot がツールを初めて使うときに VS Code がサーバーを起動し、信頼するか確認します。

## フィルタリング機能をテストする

[Playwright MCP サーバー][playwright-mcp-server]を使うと、Copilot が実際のブラウザーを操作できます。開発者がアプリを操作して変更を確認する代わりに、エージェントがページを開き、移動し、フィルターを適用し、結果を確認して、内容をまとめて報告します。会話を離れずに機能の動作を確認できます。

Playwright MCP サーバーは、スクリーンショットではなくページの[アクセシビリティツリー][playwright-mcp-server]を使います。つまり、エージェントは支援技術と同様に、ボタン、リンク、リスト項目など、ラベルの付いた構造化要素を基に動作を判断します。機能を手早く確認しながら、アクセシビリティの基本もチェックできます。

サーバーに接続し、アプリを実行した状態で、フィルタリング機能を試すよう Copilot に依頼します。

```text
Playwright MCP サーバーを使って、実行中の http://localhost:4321 にブラウザーでアクセスし、新しいゲームフィルタリング機能を確認してください。

1. ゲームページに移動し、ゲームがいくつ表示されているか確認します。
2. カテゴリーフィルターを適用し、そのカテゴリーのゲームだけが表示されることを確認します。
3. フィルターを解除してから、発行元フィルターを適用し、その発行元のゲームだけが表示されることを確認します。
4. カテゴリーと発行元のフィルターを組み合わせ、両方の条件に合う結果が表示されることを確認します。

各手順で確認したことを報告し、期待どおりに動作しない点があれば指摘してください。
```

Copilot は Playwright MCP サーバーを通じてブラウザーを起動し、各手順に沿って操作して、結果を報告します。Issue の受け入れ条件に照らして報告を確認してください。問題がある場合は、プルリクエストを開く前に、追加で質問するかコードの修正を依頼します。

> [!NOTE]
> このテストには、`http://localhost:4321` でアプリが実行されている必要があります。開発サーバーを停止した場合は、プロンプトを送る前に再起動します。Copilot が Playwright MCP サーバーを初めて使うときは、ブラウザーをダウンロードする必要がある場合があります。ブラウザーがないと報告されたら、`npx playwright install chromium` を実行して再度試すよう依頼します。

[playwright-mcp-server]: https://github.com/microsoft/playwright-mcp

## ブランチを公開してプルリクエストを作成する

機能が動作することを確認したので、チームでレビューするためのプルリクエスト (PR) を開きます。まず `filtering-vscode` ブランチを公開します。

1. Codespace の **Source Control** パネルに移動し、Copilot が加えた変更を確認します。
2. **+** アイコンを選択して変更をステージングします。
3. **Sparkle** ボタンを使ってコミットメッセージを生成します。

   ![変更内容を表示する Source Control パネルのスクリーンショット](../../../_images/ex3-source-control-changes.png)

4. **Publish** を選択してブランチをリポジトリにプッシュします。

## プルリクエストを作成する

github.com や GitHub コマンドラインインターフェイス (CLI) など、プルリクエストを作成する方法はいくつかあります。GitHub Copilot を使っているので、PR の作成を依頼します。Copilot は関連する Issue を見つけ、関連付けた PR を作成できます。

1. Copilot Chat パネルに移動し、**New Chat** を選択して新しいセッションを開始します。
2. GitHub ツールを Copilot が使えるように、エージェントドロップダウンで **Agent** が選択されていることを確認します。
3. 次のプロンプトで PR の作成を依頼します。

   ```plaintext
   リポジトリで、カテゴリーと発行元によるフィルタリングに関する Issue を見つけてください。現在のブランチから新しいプルリクエストを作成し、正しい Issue に関連付けてください。
   ```

4. Copilot が情報を収集して操作を行うために **Continue** を選択するよう求められた場合は、適宜選択して許可します。GitHub ツールを初めて使うときは、GitHub での認証を求められることがあります。プロンプトに従って認証します。
5. Copilot が Issue を検索し、該当するものを見つけて PR を作成したことを確認します。
6. Copilot が生成したリンクを選択してプルリクエストを確認します。ただし、**まだマージしないでください**。

## まとめと次のステップ

おめでとうございます。この演習では、次の作業を行いました。

- Model Context Protocol (MCP) と MCP サーバーが Copilot にツールを追加する仕組みを学びました。
- Playwright MCP サーバーをワークスペースに追加しました。
- 出荷前に、Playwright MCP サーバーを使ってブラウザー内でフィルタリング機能をテストしました。
- ブランチを公開し、フィルタリング作業のプルリクエストを作成しました。

MCP サーバーを使って機能をテストしましたが、MCP は Copilot を拡張する方法のひとつにすぎません。次は、アクセシビリティレビューなど、対象を絞ったタスクを効率化する[カスタムエージェントを作成します][next-lesson]。

## リソース

- [MCP とは何か、なぜ注目されているのか][mcp-blog-post]
- [Microsoft Playwright MCP Server][playwright-mcp-server]
- [GitHub MCP Server][github-mcp-server]
- [GitHub MCP Registry][mcp-registry]
- [VS Code の MCP サーバー][vscode-mcp-config]

[previous-lesson]: ../2-agent-mode/
[next-lesson]: ../4-custom-agents/
[mcp-blog-post]: https://github.blog/ai-and-ml/llms/what-the-heck-is-mcp-and-why-is-everyone-talking-about-it/
[github-mcp-server]: https://github.com/github/github-mcp-server
[mcp-registry]: https://github.com/mcp
[remote-github-mcp-server]: https://github.blog/changelog/2025-06-12-remote-github-mcp-server-is-now-available-in-public-preview/
[vscode-mcp-config]: https://code.visualstudio.com/docs/agents/reference/mcp-configuration
