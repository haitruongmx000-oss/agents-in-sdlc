---
title: "演習 2 - GitHub Copilot cloud agent"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 前のレッスン: カスタム指示][previous-lesson] |
|:--|

技術的負債に悩む組織は少なくありません。解決されていないセキュリティ問題、更新が必要なレガシーコード、実装する時間がなくバックログに残った機能要求などがあります。GitHub Copilot cloud agent は、コードの更新や機能の追加などのタスクを自律的に実行できます。作業が終わると、人間の開発者がレビューできるドラフト PR を作成します。これにより、面倒な作業を委任して開発を加速し、開発者はより大きな課題に集中できます。

この演習では、Copilot cloud agent を使って次の内容を確認します。

- コード生成環境をカスタマイズする。
- 操作を安全に実行する。
- 明確に範囲を定めた Issue の重要性を理解する。
- Issue を Copilot に割り当てる。

## シナリオ

Tailspin Toys には解消したい技術的負債があります。最初のサイトを作成した開発者は、ドキュメントを十分に整備しませんでした。現状ではドキュメントがほとんどありません。まず、アプリケーションのすべてのエクスポート関数に TSDoc のドキュメントコメントを追加します。

また、デザインチームはゲームを見つけやすくしたいと考えています。各ゲームの詳細ページに、同じカテゴリーの他のタイトルを「関連ゲーム」として表示し、UX の受け入れテストに使える簡易実装を希望しています。この作業は現在のブロッカーですが、ほかに優先度の高い Issue もあります。

こうしたタスクは優先順位が下がりやすく、Copilot cloud agent に割り当てるのに適しています。Copilot cloud agent が非同期で作業している間、開発者は別のタスクを進め、後から結果をレビューできます。

## GitHub Copilot cloud agent について

[GitHub Copilot cloud agent](https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent#overview-of-copilot-cloud-agent-formerly-copilot-coding-agent) は、人間の開発者のようにバックグラウンドでタスクを実行できます。たとえば、[GitHub Issue を Copilot に割り当てる][assign-issue]方法があります。割り当て後、Copilot は進行状況を追跡するドラフトプルリクエスト (PR) を作成し、環境を設定して作業を始めます。作業中も完了後もセッションを確認できます。提案された解決策のレビュー準備ができると、プルリクエストで開発者に通知します。

## 範囲を適切に定めた指示の重要性

GitHub Copilot に魔法はありません。考える作業を省略できるわけではありません。単純に見える操作でも、詳しく調べると複雑なことがあります。[Copilot cloud agent に依頼するタスクの範囲を慎重に決めてください][cloud-agent-best-practices]。AI ペアプログラマーとして扱い、段階的に進め、学び、試し、状況に応じて調整します。生成 AI を導入しても、ソフトウェア開発の基本は変わりません。

## このリポジトリのカスタム指示

前の演習では、カスタム指示と Copilot への影響を確認しました。カスタム指示の演習をすでに行っていれば、`.github/copilot-instructions.md` が機能することを確認済みです。まだの場合は、cloud agent に作業を割り当てる前に、このリポジトリに含まれる指示ファイルを参照のみで確認し、後からその影響を確認できるようにします。

リポジトリの GitHub Web UI または起動済みの Codespace で、次のファイルを開きます。

- `.github/copilot-instructions.md`: **Code standards** セクション、特に TypeScript の慣例と TSDoc のドキュメントコメントに関する要件を確認します。
- `.github/instructions/unit-tests.instructions.md`: `applyTo` フロントマターが `**/*.test.ts` に一致するファイルに指示を適用することを確認します。

次のセクションで `コードにドキュメントが不足している` Issue を cloud agent に割り当てます。生成されたプルリクエストでは、これらの指示ファイルに基づく TSDoc のドキュメントコメント、TypeScript の慣例、コメントヘッダーを確認してください。後の演習で PR をレビューするときに、改めて説明します。

## Copilot cloud agent の開発環境をセットアップする

コードを作成する際は、通常、適切な環境とセットアップスクリプトが必要です。Copilot にタスクを割り当てる場合も同じです。cloud agent はソフトウェアエンジニアのように作業します。

cloud agent は作業環境に [GitHub Actions][github-actions] を使います。`.github/workflows/copilot-setup-steps.yml` に設定する[専用のセットアップワークフロー][setup-workflow]を作成すると、作業開始前に必要な開発ツールや依存関係をインストールできます。ラボを進めやすくするため、このワークフローは事前に構成されています。単一の Astro アプリのために、Node.js、プロジェクトの依存関係、エンドツーエンドテスト用のブラウザーバイナリ、マイグレーションおよびシード済みの SQLite データベースを用意します。

```yaml
name: "Copilot Setup Steps"

# Allows you to test the setup steps from your repository's "Actions" tab
on: workflow_dispatch

env:
  ASTRO_TELEMETRY_DISABLED: "1"

jobs:
  copilot-setup-steps:
    runs-on: ubuntu-latest
    # Set the permissions to the lowest permissions possible needed for *your steps*. Copilot will be given its own token for its operations.
    permissions:
      # If you want to clone the repository as part of your setup steps, for example to install dependencies, you'll need the `contents: read` permission. If you don't clone the repository in your setup steps, Copilot will do this for you automatically after the steps complete.
      contents: read
    steps:
      - name: Checkout code
        uses: actions/checkout@v5

      # Frontend / app setup - Node.js (the whole app is now Astro + Drizzle/libSQL)
      - name: Set up Node.js
        uses: actions/setup-node@v6
        with:
          node-version: "lts/*"
          cache: "npm"
          cache-dependency-path: "./package-lock.json"

      - name: Install JavaScript dependencies
        run: npm ci

      - name: Install Playwright browsers
        run: npx playwright install --with-deps chromium

      # Migrate + seed the local SQLite database so builds and tests have data.
      - name: Set up the database
        run: npm run db:setup
```

一般的な GitHub ワークフローと同じ形式ですが、次の点に注意してください。

- `copilot-setup-steps` という 1 つのジョブがあります。このジョブは、Copilot がプルリクエスト作業を始める前に GitHub Actions で実行されます。
- `workflow_dispatch` トリガーが設定されています。リポジトリの **Actions** タブからワークフローを手動実行できるため、Copilot の作業を待たずに実行結果を確認できます。

## ドキュメントを追加する

ドキュメントが重要だと理解していても、多くのプロジェクトでは情報が古かったり不足していたりします。この種の技術的負債は放置されやすく、生産性が低下し、コードベースの保守や新しい開発者の参加が難しくなります。ドキュメントの作成は Copilot が得意とする作業なので、Copilot cloud agent に適した Issue です。バックグラウンドで必要なドキュメントを作成します。後の演習で結果をレビューします。

1. 新しいブラウザータブで github.com のリポジトリに移動します。
2. **Issues** タブを選択します。
3. **New issue** を選択して、新しい Issue のダイアログを開きます。
4. **Blank issue** を選択して、新しい Issue を作成します。
5. **Title** に `コードにドキュメントが不足している` と入力します。
6. **Description** に次の内容を入力します。

   ```plaintext
   関数やメソッドには、役立つ場合に TSDoc のドキュメントコメントを含めることが組織の要件です。しかし、最近の更新ではこの標準が守られていません。動作を明確にするドキュメントコメントが含まれるよう、既存のコードを更新する必要があります。
   ```

7. **Create** を選択して Issue を作成します。
8. 右側の **Assign to Copilot** を選択し、割り当てダイアログを開きます。

   ![Issue に Copilot を割り当てる](../../../_images/shared-assign-copilot.png)

9. **Assign** を選択します。
10. **Pull Requests** タブを選択します。
11. 新しく作成されたプルリクエスト (PR) を開きます。タイトルは `[WIP]: コードにドキュメントが不足している` のような名前になります。一覧に PR が表示されない場合は、少し待ってからブラウザーを再読み込みします。
12. 数分後、Copilot が作成した To Do リストが表示されます。

> [!NOTE]
> Copilot の To Do リストが PR に表示されるまで、数分かかる場合があります。Copilot は、前述のワークフローを実行して環境を作成し、プロジェクトを分析して、問題への最適な取り組み方を検討しています。

13. To Do リストを確認し、Copilot が実行するタスクを把握します。
14. プルリクエストのタイムラインを下にスクロールし、Copilot が Issue の作業を開始したことを示す更新を確認します。
15. **View session** ボタンを選択します。

   ![Copilot のセッションビュー](../../../_images/ex4-view-session.png)

> [!CAUTION]
> 状態表示を更新するため、ブラウザーを再読み込みする必要がある場合があります。

16. ライブセッションをスクロールし、Copilot が問題に取り組む様子を確認します。コードを調査して現在の状態を把握し、計画を考えて判断し、コードを作成する作業を確認できます。

この処理には数分かかります。Copilot cloud agent の主な目的のひとつは、非同期に作業して開発者が別のタスクに集中できるようにすることです。この利点を活用するため、cloud agent に別のタスクを割り当ててから、アプリケーションの機能を追加する作業に移ります。

## ゲーム詳細ページに関連ゲームのセクションを追加する

GitHub Copilot cloud agent の大きな利点は、作業を分担できることです。デザインチーム向けに関連ゲームのセクションを追加する作業が短時間で終わる場合でも、その時間を別の作業に使えます。Copilot cloud agent に割り当てます。

1. github.com のリポジトリに戻ります。
2. **Issues** タブを選択します。
3. **New issue** を選択して、新しい Issue のダイアログを開きます。
4. **Blank issue** を選択して、空のテンプレートを使います。
5. **Title** に `ゲーム詳細ページに関連ゲームを表示する` と入力します。
6. **Description** に次の内容を入力します。

   ```markdown
   各ゲームの詳細ページに関連ゲームを表示し、ほかのゲームを見つけやすくしたいと考えています。デザインチームはユーザーエクスペリエンス (UX) を検討し、受け入れテストを行いたいと考えています。要件は次のとおりです。

   - 指定されたゲーム自身を除き、同じカテゴリーの他のゲームを返すデータアクセスヘルパーを `src/lib/` に追加する。
   - そのヘルパーを使う「関連ゲーム」セクションをゲーム詳細ページに表示する。
   - 関連ゲームがない場合に対応する。
   - 新しいヘルパーの単体テストを作成する。
   - PR を作成する前に、すべてのテストに合格することを確認する。
   ```

7. **Create** を選択して Issue を作成します。
8. 右側の **Assign to Copilot** を選択し、割り当てダイアログを開きます。

   ![Issue に Copilot を割り当てる](../../../_images/shared-assign-copilot.png)

9. **Assign** を選択します。

まもなく、Copilot が作業を開始したことを示す 👀 が Issue の最初のコメントに表示されます。

![Copilot が Issue に取り組んでいることを示す目の絵文字](../../../_images/ex4-issue-eyes-emoji.png)

Copilot は 2 つ目の依頼に取り組んでいます。Copilot cloud agent はソフトウェアエンジニアと同じように動作するため、作業を積極的に監視する必要はなく、完了後にレビューできます。次は、カスタムエージェントの作成と使用を確認します。

## まとめと次のステップ

[GitHub Copilot cloud agent][copilot-agents] を確認しました。cloud agent に Issue を割り当てると、非同期で作業を実行できます。技術的負債への対応、新機能の作成、フレームワーク間のコード移行などに利用できます。

この演習では、次の内容を確認しました。

- コード生成環境をカスタマイズする。
- 操作を安全に実行する。
- 明確に範囲を定めた Issue の重要性を理解する。
- Issue を Copilot に割り当てる。

Copilot がバックグラウンドで作業する間、カスタムエージェントの作成と利用に進みます。[Copilot cloud agent は MCP サーバー][cloud-agent-mcp]とカスタム指示も利用できます。前の演習でカスタム指示を確認しました。

## リソース

- [Copilot cloud agent について][copilot-agents]
- [GitHub Issue を Copilot に割り当てる][assign-issue]
- [Copilot cloud agent セットアップワークフローのベストプラクティス][cloud-agent-best-practices]

| [次のレッスン: カスタムエージェント →][next-lesson] |
|--:|

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-custom-agents/
[cloud-agent-mcp]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/extend-cloud-agent-with-mcp
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[github-actions]: https://docs.github.com/actions
