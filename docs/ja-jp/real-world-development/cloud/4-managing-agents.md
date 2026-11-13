---
title: "演習 4 - エージェントの監視と管理"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

これまでの演習では、ユーザーエクスペリエンスの改善と機能追加を目的に、GitHub Copilot cloud agent に 3 つのタスクを依頼しました。cloud agent は非同期かつ自律的に動作しますが、タスクを監視することも重要です。

cloud agent に割り当てたタスクは、GitHub.com の[エージェントページ][agents-page]など、さまざまなツールで管理できます。この Mission control では、オープンプルリクエスト (PR) があるすべてのエージェントタスクを確認できます。実行された操作を調べたり、進行中のセッションを誘導したりできます。

このレッスンでは、次の内容を学習します。

- エージェントページを開き、cloud agent のタスクを監視する。
- 進行中のセッションを誘導し、機能の追加を依頼する。

## シナリオ

ハイコントラストモードの作成をエージェントに依頼した後、チームはライトモードも追加したいと考えました。サイトのスタイル更新と切り替え機能を実装中のため、これも含めるのが自然です。エージェントの作業を誘導し、ハイコントラストに加えてライトモードも追加します。

## Copilot cloud agent のタスクを確認する

Copilot cloud agent に割り当てたタスクの現在の状態を確認します。

1. [Copilot agents ページ](https://github.com/copilot/agents)に移動します。
2. メインペインと左ペインのタスクリストを確認します。次のような、Copilot に割り当てたタスクが表示されます。
   - コードベースのドキュメント更新。
   - ゲーム変更用 API の生成。
   - Web サイトへのハイコントラストモード追加。
3. 実行中のタスクを選択し、Copilot が行った作業を確認します。次のような操作が含まれる場合があります。
   - リポジトリからコードをチェックアウトする。
   - Copilot の作業環境を作成する。
   - MCP サーバーをセットアップする。
   - 割り当てられたタスクを完了するための各種手順を実行する。

> [!NOTE]
> 表示される具体的な手順は、Copilot の作業状況や進め方によって異なります。

4. 右側に表示されるプルリクエスト (PR) ペインも確認します。ここでは、PR と変更されたファイルを確認できます。

## cloud agent を誘導する

実行中のタスクを確認できたので、ハイコントラストモードの作業中にライトモードの切り替えも追加するよう Copilot に依頼します。

1. ハイコントラストモードの追加に関するセッションを選択します。タイトルは Copilot が付けた名前や作業状況によって異なります。

   ![Mission control のアクセシビリティセッション](../../../_images/ex6-accessibility-session.png)

2. セットアップが完了し、作業が始まるまで数分間セッションを確認します。作業開始後は、次のようなメッセージが表示されます。
3. **Steer active session while Copilot is working** ダイアログに次のプロンプトを入力します。

   ```plaintext
   ハイコントラストモードを作成するのに合わせて、ライトモードも追加してください。このモード用の切り替え機能も用意し、ユーザーが希望する表示モードを選べるようにしてください。
   ```

   ![Copilot が作業中のエージェントページで、Steer active session while Copilot is working ダイアログが強調表示された画面](../../../_images/ex6-steer-cloud-agent-task.png)

4. <kbd>Enter</kbd> を押してプロンプトを送信します。
5. Copilot がプロンプトを認識し、作業に取り込んだことを確認します。

## Copilot に作業を続けさせる

Copilot は更新されたタスクに取り組みます。メッセージを送信した時点で実行中の手順を完了した後、新しい依頼を作業の流れに取り込みます。

前回と同様に数分かかります。これまでに学んだことを振り返りながら待ちます。

## まとめと次のステップ

GitHub Copilot cloud agent タスクの監視と誘導に使う中心的な場所、Copilot agents ページを確認しました。この Mission control を使って、実行中および完了したすべてのタスクを追跡し、作業内容をレビューし、進行中のタスクをリダイレクトして範囲の変更や追加の指示を行えます。

このレッスンでは、次の内容を確認しました。

- Mission control とエージェントページで cloud agent のタスクを監視しました。
- 進行中のセッションをリダイレクトし、機能の追加を依頼しました。

Copilot がアクセシビリティ機能を実装している間に、[作成されたプルリクエストを反復的に改善します][next-lesson]。

## リソース

- [Copilot agents ページ][agents-page]
- [カスタムエージェントについて][custom-agents]

---

| [← 前のレッスン: カスタムエージェント][previous-lesson] | [次のレッスン: Copilot の作業を反復する →][next-lesson] |
|:--|--:|

[previous-lesson]: ../3-custom-agents/
[next-lesson]: ../5-iterating/
[agents-page]: https://github.blog/changelog/2025-07-03-agents-page-for-copilot-coding-agent-in-public-preview
[custom-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
