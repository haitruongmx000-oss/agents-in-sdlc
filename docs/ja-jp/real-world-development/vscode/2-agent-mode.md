---
title: "演習 2 - Copilot Agent Mode で機能を追加する"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

アプリケーションの単純な更新でも、通常は複数のファイルを変更し、テストを実行する必要があります。開発者は必要なファイルを見つけて変更し、テストを実行し、問題をデバッグして、変更が漏れたファイルを探し、さらに修正するという作業を繰り返します。

このような作業には Copilot Agent Mode が役立ちます。

Copilot Agent Mode は統合開発環境 (IDE) 内でより自律的に作業します。開発者のように既存のプロジェクト構成を調べ、必要な変更を行い、テストなどのタスクを実行し、見つかった問題を自動的に修正します。Agent Mode を使って、サイトに新しい機能を追加する方法を確認します。

> [!NOTE]
> 名前は似ていますが、agent mode と cloud agent は異なる用途向けに作られています。agent mode は IDE 内で作業し、素早くフィードバックを得て対話できます。cloud agent はチームメンバーのように非同期で動き、Issue とプルリクエストを通じてやり取りします。

この演習では、次の内容を学習します。

- Copilot Agent Mode がプロジェクトを調べ、関連ファイルを特定し、複数のファイルにまたがる変更を行う方法。
- GitHub Copilot Agent Mode でユーザーインターフェイス (UI) とデータレイヤーに新機能を実装する方法。
- コードベースにマージする前に、Copilot Agent Mode が生成した変更とテストをレビューする方法。

## シナリオ

ゲーム一覧が増えているため、ユーザーがカテゴリーと発行元で絞り込めるようにします。前の演習では発行元ヘルパーを追加しました。次は、Copilot Agent Mode を使って残りのデータレイヤー、UI、テストを完成させます。

## Tailspin Toys Web サイトを実行する

変更を加える前に、Tailspin Toys Web サイトの現在の機能を確認します。

この Web サイトは、開発者をテーマにしたボードゲームのクラウドファンディングプラットフォームです。ゲームを一覧表示し、詳細を表示できます。単一の Astro アプリで、ビルド時にページを静的な HyperText Markup Language (HTML) としてレンダリングします。ページは frontmatter で Drizzle ORM を通じてローカル SQLite データベースを直接参照します。独立したバックエンドのアプリケーション・プログラミング・インターフェイス (API) やクライアント側 UI フレームワークはありません。再利用可能なデータアクセスヘルパーは `src/lib/` に置き、インタラクティブな操作は標準の Document Object Model (DOM) API を使う小さなスコープ付き Astro `<script>` で追加します。

### Web サイトを起動する

Web サイトを簡単に実行できるよう、単一の Astro 開発サーバーを起動する `npm run dev` タスクが用意されています。GitHub Codespace で次の手順を実行します。

1. Codespace に戻ります。前の演習の後なので、`main` にいるはずです。
2. Codespace 内で <kbd>Control</kbd>+<kbd>\`</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>\`</kbd> (Windows/Linux) を押して新しいターミナルウィンドウを開きます。
3. フィルタリング用の新しいブランチを作成して切り替えます。

   ```bash
   git checkout main
   git pull
   git checkout -b filtering-vscode
   ```

4. 次のコマンドで Web サイトを起動します。

   ```bash
   npm run dev
   ```

   Astro 開発サーバーの準備ができると、次のような URL を示すメッセージが表示されます。

   ```bash
   🚀 Tailspin Toys is ready!
      Astro server: http://localhost:4321
      Press Ctrl-C to stop.
   ```

> [!NOTE]
> `http://localhost:4321` を開くよう促すダイアログが表示された場合は、**x** を選択して閉じます。

5. ターミナルで <kbd>Command</kbd> (Mac) または <kbd>Ctrl</kbd> (Windows/Linux) を押したまま、Astro サーバーのアドレス `http://localhost:4321` を選択して Web サイトを開きます。

> [!NOTE]
> Codespace のターミナルから localhost URL のリンクを選択すると、自動的に `https://<your-codespace-name>-4321.app.github.dev/` にリダイレクトされます。これは Codespace でホストされている Web サーバーへのプライベートトンネルです。

### Web サイトを確認する

Web サイトを起動したら、その機能を確認できます。主な機能は次のとおりです。

- **ホームページ**: タイトル、画像、説明を含むボードゲームの一覧を表示します。
- **ゲーム詳細ページ**: ゲームを選択すると、タイトル、説明、発行元、カテゴリーなどの情報を表示する詳細ページが開きます。

## Copilot でバックログを確認する

> [!TIP]
> **Copilot Chat を開く**
>
> 以下の演習を始める前に Codespace に戻り、Copilot Chat パネルを開き、**New Chat** を選択して新しい会話を始めます。モードとモデルの選択は演習によって異なります。必要な場合は、各手順で説明します。

Web サイトの初期実装は動作しますが、機能を追加します。まずバックログを確認します。テンプレートからプロジェクトをセットアップすると、Issue のバックログが自動作成されています。GitHub Copilot にそれらの項目を表示するよう依頼します。

1. Chat ビューのエージェントドロップダウンから **Agent** を選択します。**Agent** エージェントは、自律的に計画を立て、複数ファイルに変更を加え、ターミナルコマンドを実行し、ツールを呼び出します。

   ![Chat ビューのエージェント選択を示すスクリーンショット。](../../../_images/shared-chat-mode-selector.png)

2. 利用可能なモデル一覧から **Claude Sonnet 4.5** を選択します。

> [!CAUTION]
> このワークショップの作成者は、特定のモデルを推奨しているわけではありません。ワークショップの作成時に Claude Sonnet 4.5 を使ったため、手順に含めています。コードの提案をある程度一貫させ、良い体験を提供することが目的です。ただし、大規模言語モデル (LLM) は確率的に動作するため、ワークショップで示す内容と異なる提案が表示されることがあります。これは通常の動作です。

> [!NOTE]
> 大規模言語モデル (LLM) の確率的な性質により、Copilot が異なる MCP コマンドを使う場合がありますが、タスク自体は完了できるはずです。

3. 次のプロンプトを Copilot に送信し、バックログについて尋ねます。

   ```plaintext
   GitHub リポジトリのバックログを表示してください。ユーザーにとって最も役立つ項目を優先して、順番を提案してください。
   ```

4. すべての Issue を一覧表示するコマンドを実行するため、**Continue** を選択します。
5. 生成された Issue の一覧を確認します。

Copilot がユーザーにとって役立つと思われる項目を優先順位付けしたことを確認します。

## 指示ファイルを確認する

Copilot にコードを生成させる前に、作業のコンテキストを与えるための指示ファイルを確認します。Web サイトの機能追加への取り組み方を説明する[ユーザーインターフェイスの指示ファイル](https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md)を使います。

1. Codespace で `.github/instructions/ui.instructions.md` に移動します。
2. 機能追加に関する全体的なガイダンスを確認します。次の内容が含まれます。
   - アーキテクチャの概要。
   - コンポーネントの設計、テスト容易性、アクセシビリティの原則。
   - Astro、Tailwind CSS、Drizzle などのファイル種類ごとの指示ファイルへのリンク。

> [!TIP]
> 指示ファイルから、他の指示ファイルやプロジェクト内ファイルを参照できます。パスは指示ファイルの場所からの相対パスです。この機能を使って指示を再利用し、大きな指示を管理しやすい小さなファイルに分け、参考になる例やひな型を示せます。

## フィルタリング機能を実装する

フィルタリングを完成させるには、少なくとも次の 3 つを更新する必要があります。

- データレイヤー (`src/lib/`) にフィルタリングロジックを追加するか、既存のロジックを調整する。
- フィルタリングの動作に関するテストを追加または更新する。
- ゲーム一覧ページにフィルタリング UI を追加する。

コードベースにマージする前に、テストを実行して合格する必要もあります。Copilot Agent Mode にこれらのタスクを依頼します。

1. Copilot との現在の会話を続けるか、**New Chat** を選択して新しい会話を始めます。
2. **Add Context**、**Instructions**、**ui** の順に選択し、指示ファイルを追加します。

   ![UI 指示ファイルを選択する例を示すスクリーンショット](../../../_images/ex3-select-instructions-file.png)

3. Chat ビューのエージェントドロップダウンで **Agent** が選択されていることを確認します。

   ![Chat ビューのエージェント選択を示すスクリーンショット。](../../../_images/shared-chat-mode-selector.png)

4. モデルが **Claude Sonnet 4.5** のままであることを確認します。
5. バックログの関連 Issue に基づいて機能を実装するよう、次のプロンプトで Copilot に依頼します。

   ```plaintext
   関連する GitHub Issue の要件に基づいて、発行元とカテゴリーによるフィルタリングをサイトに追加してください。前の演習で作成した発行元ヘルパーを保持し、必要に応じて調整しながら、残りのデータレイヤー、UI、テストを完成させてください。完了前にすべてのテストが合格することを確認してください。サーバーはすでに実行中なので、起動する必要はありません。
   ```

6. Copilot がプロジェクトを調べ、機能に関連するファイルを探す様子を確認します。データレイヤーのヘルパー、UI、テストを見つけて、ファイルを変更し、テストを実行します。

   ![Copilot がプロジェクトファイルを調査している様子](../../../_images/ex3-agent-mode-explores.png)

> [!NOTE]
> Copilot はプロジェクトの調査、ファイルの変更、テストの実行など、複数のタスクを行います。タスクやコードベースの複雑さによっては、数分かかることがあります。作業中、コードエディターに **Keep** と **Undo** が表示される場合があります。Copilot の作業が完了すると、すべての変更に対して **Keep** または **Undo** を選択できます。作業中に選択する必要はありません。

7. Copilot に求められたら、**Continue** を選択してテストを実行します。

   ![Copilot Chat ペインでテスト実行の確認を求めるダイアログ](../../../_images/ex3-agent-mode-run-tests.png)

8. 作業中に一時停止したり、テストが失敗したりする場合があります。問題ありません。Copilot はコード生成とテストを繰り返し、エラーがないと判断するまで作業を続けます。

   ![Copilot Agent Mode の完了した Chat セッション](../../../_images/ex3-agent-mode-proposed-changes.png)

9. 生成されたコードに問題がないか確認します。

> [!CAUTION]
> Copilot や他の AI ツールが生成したコードは、必ずレビューしてください。

10. Web サイトが起動しているブラウザーに戻り、新しい機能を試します。
11. 動作とコードを確認したら、Copilot Chat ウィンドウで **Keep** を選択します。

## まとめと次のステップ

おめでとうございます。この演習では、GitHub Copilot Agent Mode を使って Tailspin Toys Web サイトに機能を追加する方法を確認しました。次の内容を学びました。

- GitHub Copilot Agent Mode で UI とデータレイヤーに新機能を実装する方法。
- Copilot Agent Mode がプロジェクトを調べ、関連ファイルを特定し、複数のファイルにまたがる変更を行う方法。
- コードベースにマージする前に、Copilot Agent Mode が生成した変更とテストをレビューする方法。

次は、[Playwright MCP サーバーで機能をテストし][next-lesson]、プルリクエストを作成します。

### 追加の探索演習 — ページネーションを実装する

ゲーム一覧が増えたら、ページネーションが必要になります。この演習で学んだことを使い、Copilot にサイトのページネーション実装を依頼します。次の点を検討してください。

- 既存の指示ファイルを使うなど、現在のベストプラクティスに従う。
- ページサイズをユーザーが選べるようにするか、固定にするかなど、ページネーションの動作を決める。
- 希望する実装に必要なガイダンスをプロンプトに含める。
- Copilot に変更を依頼し、コンテキストを提供しながら反復する必要がある場合があります。これは Copilot と作業する通常の流れです。

## リソース

- [Copilot の ask、edit、agent mode: それぞれの役割と使い分け][choose-mode]
- [VS Code の agent mode][vs-code-agent-mode]

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-mcp/
[choose-mode]: https://github.blog/ai-and-ml/github-copilot/copilot-ask-edit-and-agent-modes-what-they-do-and-when-to-use-them/
[vs-code-agent-mode]: https://code.visualstudio.com/docs/copilot/chat/chat-agent-mode
