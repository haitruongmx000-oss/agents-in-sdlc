---
title: "レッスン 4 - カスタム指示で Copilot を導く"
description: "リポジトリの指示を確認し、ドキュメント標準を追加して、フィルタリングのコードに適用します。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

人工知能 (AI) を使うときは、コンテキストが重要です。特定の方法でタスクを進める必要があるなら、その指針を Copilot が利用できるようにします。[指示ファイル][instruction-files]には、求めるコードだけでなく、その構成方法も記述できます。フィルタリングを実装したので、Copilot が使った指示を確認し、ドキュメント標準を追加して、コードに適用します。

このレッスンでは、次の内容を学習します。

- リポジトリの指示とパスで対象を限定した指示ファイルがエージェントに届く仕組みを確認する。
- コーディング標準に従うため、指示ファイルを更新する。
- 指示ファイルがコードに与える影響を確認する。

## シナリオ

Tailspin Toys には、開発に関するガイドラインと要件があります。具体的には、次のとおりです。

- コメントでは、コードの内容を繰り返すのではなく、意図や自明でない判断を説明します。
- `db/` と `src/lib/` のエクスポート関数には、目的、パラメーター、戻り値を説明する TSDoc/JSDoc を記述します。該当する場合は、注入可能な `db` 引数も含めます。
- 再利用可能な Astro コンポーネントには `Props` の契約を記述し、関連するコードの変更時にはコメントを最新の状態に保ちます。
- 既存のフォーマットと lint のガイダンスを維持します。

指示ファイルを使い、これらの慣例に沿ってタスクを実行するための情報を Copilot に提供します。

## 指示ファイル

カスタム指示を使うと、コーディングスタイルや要件などのコンテキストを Copilot に提供でき、より適切な提案やコードスニペットを得やすくなります。好みの規約、ライブラリ、コメントの書き方などを指定できます。リポジトリ全体に適用する指示も、特定の種類のファイルだけに適用する指示も作成できます。

指示ファイルには次の 2 種類があります。

- `.github/copilot-instructions.md`: リポジトリに対するすべての要求で Copilot に送られる、単一の指示ファイルです。大半の要求に関係するプロジェクト全体の情報を記述します。
- `.github/instructions/*.instructions.md`: 特定の言語、ファイルの種類、またはタスクに関するガイドラインを記述します。

> [!NOTE]
> 他の指示形式やサポート状況は、環境によって異なります。特定の形式を利用する前に、[カスタム指示のサポートに関するリファレンス][custom-instructions-support]を確認してください。

## このプロジェクトのカスタム指示ファイルを確認する

スタータープロジェクトには、すでに指示ファイルが含まれています。変更を加える前に内容を確認し、既存の指示がどう影響するかを確かめます。

1. Codespace に戻ります。
2. Codespaces エディターで (ターミナルではなく) `.github/copilot-instructions.md` を開きます。
3. プロジェクトの簡単な説明とコーディングガイダンスを確認します。この指示は、リポジトリ内で Copilot を使うすべての操作に適用されます。
4. `.github/instructions` フォルダーを開き、ファイルを確認します。Astro ファイル、Drizzle データレイヤー、テストなどに関する指示があります。
5. `.github/instructions/unit-tests.instructions.md` を開きます。先頭の `applyTo` フィールドが、適用対象を決める glob であることを確認します。
6. `.github/instructions/drizzle.instructions.md` を開き、他の指示ファイルや既存プロジェクトファイルへのリンクを確認します。指示を小さく再利用可能なファイルに分け、参考例を Copilot に示せます。

## チームのガイダンスに合わせて指示ファイルを更新する

既存の指示は十分な出発点ですが、追加で対応したい内容があります。新たに生成する TypeScript に TSDoc コメントを追加するため、基本となる `copilot-instructions.md` を変更します。

1. `.github/copilot-instructions.md` で、35 行目付近にある **Code formatting guidance** セクションを探します。
2. 最後の箇条書きとして、次の行を追加します。

   ```markdown
   - All new TypeScript should contain TSDocs comments for documentation purposes.
   ```

ファイルは自動的に保存されます。

## 更新したガイダンスを使う

Copilot CLI は会話開始時にリポジトリの指示を読み込みます。指示を保存した後、フィルタリングの会話を終了して再開すると、機能のコンテキストを保ったまま新しい指示を読み込めます。

1. フィルタリングの会話がまだ開いている場合は、`/exit` を入力して終了します。Copilot CLI は会話を自動的に保存します。
2. `game-filters-cli` ブランチのリポジトリルートで、保存した会話を再開します。

   ```bash
   copilot --resume
   ```

   選択を求められたらフィルタリングの会話を選び、`game-filters-cli` ブランチがチェックアウトされていることを確認します。
3. Copilot に更新済みのガイダンスを適用するよう依頼します。

   ```plaintext
   指示とコードガイダンスを更新しました。生成したコードをそのガイダンスに合わせて更新してください。
   ```

4. `/diff` を入力して、変更された TypeScript ファイルを確認します。新しい TSDoc コメントが追加され、コードを正確に説明していることを確認します。

## まとめと次のステップ

Copilot CLI が指示ファイルからコンテキストを取得する仕組みを確認し、新しい標準を機能に適用しました。次は、再利用可能な [quality-checks スキルをカスタマイズして実行し][next-lesson]、lint とテストを一貫して実施します。

## リソース

- [Copilot CLI のカスタム指示を追加する][instruction-files]
- [カスタム指示の作成に関するベストプラクティス][instructions-best-practices]
- [効果的なカスタム指示を作成するためのベストプラクティス][awesome-copilot]

[instruction-files]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot/add-custom-instructions
[instructions-best-practices]: https://docs.github.com/copilot/concepts/prompting/response-customization#writing-effective-custom-instructions
[awesome-copilot]: https://awesome-copilot.github.com/
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support
[previous-lesson]: ../3-agent-modes/
[next-lesson]: ../5-agent-skills/
