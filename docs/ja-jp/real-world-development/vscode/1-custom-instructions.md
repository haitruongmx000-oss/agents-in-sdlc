---
title: "演習 1 - カスタム指示 (VS Code)"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 前のレッスン: 前提条件][previous-lesson] · [次のレッスン: Agent mode →][next-lesson]

人工知能 (AI) を使うときは、コンテキストが重要です。特定の方法でタスクを進める必要がある場合や、Copilot に伝えておくべき背景情報がある場合は、そのコンテキストを利用できるようにします。Copilot と特定のコンテキストを共有する方法はいくつかあります。中でも重要なのが、コード生成の指針を伝える[指示ファイル][instruction-files]です。

この演習では、次の内容を学習します。

- リポジトリのカスタム指示とパスで対象を限定した指示ファイルによって、プロジェクト固有のコンテキスト、コーディングガイドライン、ドキュメント標準が Copilot に伝わる仕組みを確認する。
- 現在の指示を使って、フィルタリングに必要な最初のデータスライス (発行元ヘルパー) を生成する。
- `.github/copilot-instructions.md` に新しいリポジトリ全体の標準を追加する。
- 同じプロンプトを再実行し、生成コードが新しい標準に従うことを確認する。
- 指示の更新とフィルタリングの最初のデータスライスを `main` にコミットし、次の演習で更新したガイダンスを使えるようにする。

> [!CAUTION]
> 生成コードが設定した標準から逸脱する場合があります。Copilot は確率的に動作します。この演習では、出力を一字一句一致させるのではなく、指示の更新後に動作がどう変わるかを確認します。

## 指示ファイル

### シナリオ

Tailspin Toys には、開発に関するガイドラインと要件があります。具体的には、次のとおりです。

- データレイヤーには、必ず単体テストを追加します。
- ユーザーインターフェイス (UI) はダークモードにし、現代的な見た目にします。
- コードには TSDoc のドキュメントコメントを追加します。
- 各ファイルの先頭に、そのファイルの役割を説明するコメントブロックを追加します。

指示ファイルを使い、これらの慣例に沿ってタスクを実行するための情報を Copilot に提供します。

### カスタム指示

カスタム指示を使うと、コーディングスタイルや要件などのコンテキストを Copilot に提供でき、より適切な提案やコードスニペットを得やすくなります。好みの規約、ライブラリ、コメントの書き方などを指定できます。リポジトリ全体に適用する指示も、特定の種類のファイルだけに適用する指示も作成できます。

指示ファイルには次の 2 種類があります。

- `.github/copilot-instructions.md` は、リポジトリへの **すべての** 要求で Copilot に送信される単一の指示ファイルです。技術スタック、構築するものの概要、ベストプラクティスなど、大半のチャットや CLI の要求に関係するプロジェクト全体の情報を記述します。
- `.github/instructions/*.instructions.md` は、特定のタスクやファイルの種類に指針を適用するためのファイルです。TypeScript や Astro などの言語、UI コンポーネントの作成や単体テストの追加などのタスクに関するガイドラインを記述できます。

> [!NOTE]
> 統合開発環境 (IDE) では、指示ファイルは Copilot Chat のコード生成にのみ使われ、コード補完や次の編集候補には使われません。
>
> Copilot Chat、Copilot CLI、Copilot cloud agent は、コード生成時にリポジトリレベルの指示と、`applyTo` フロントマターがある `*.instructions.md` ファイルを使用します。
>
> Copilot は、AGENTS.md や CLAUDE.md など、ほかの標準形式の指示ファイルにも対応しています。[カスタム指示のサポート][custom-instructions-support]を確認してください。

### 指示ファイルを管理するためのベストプラクティス

指示ファイルの作成に関する詳細な説明は、このワークショップの範囲外です。ただし、サンプルプロジェクトの例から、一般的なアプローチを確認できます。

- `copilot-instructions.md` には、構築するものの説明、プロジェクト構成、全体的なコーディング標準など、プロジェクトレベルのガイダンスを記述します。
- `*.instructions.md` には、単体テスト、Astro コンポーネント、データレイヤーなどのファイル種類や、特定のタスクに関する指示を記述します。
- 自然な言葉を使い、分かりやすいガイダンスを記述します。コードの良い例と悪い例を示します。

指示ファイルを作成する唯一の正解はありません。AI の使い方と同様に、プロジェクトに合った方法を試しながら見つけてください。

> [!TIP]
> GitHub Copilot を使うプロジェクトには、充実した指示ファイルを用意することをお勧めします。このプロジェクトには、[UI 更新][ui-instructions]や [Astro][astro-instructions] など、さまざまなタスク向けの指示ファイルがあります。
>
> Copilot に指示ファイルを作成させることもできます。VS Code の **Configure Chat → Generate Agent Instructions** や Copilot CLI の `/init` など、各環境で手順が異なります。利用中の環境に関するレッスンで説明します。
>
> テンプレートや出発点を探す場合は、指示ファイル、カスタムエージェント、その他のリソースを集めた [awesome-copilot][awesome-copilot] を確認してください。

[ui-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md
[astro-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/astro.instructions.md
[awesome-copilot]: https://github.com/github/awesome-copilot
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support

## このプロジェクトのカスタム指示ファイルを確認する

リポジトリに含まれる指示ファイルを確認します。基本となる `copilot-instructions.md` と、さまざまなタスク向けの `*.instructions.md` ファイルがあります。エディターまたは GitHub Web UI で開きます。

1. `.github/copilot-instructions.md` を開きます。
2. プロジェクトの簡単な説明と、**Agent notes**、**Code standards**、**Scripts**、**Repository Structure** などのセクションを確認します。**Code standards** の下には **GitHub Actions Workflows** の指針もあります。これらは Copilot を使うすべての操作に適用されます。
3. `.github/instructions` フォルダーを開いて内容を確認します。Astro ファイル、Drizzle データレイヤー、テストなどに関する指示があります。
4. `.github/instructions/unit-tests.instructions.md` を開きます。先頭の `applyTo` フィールドが、リポジトリルートを基準とする glob であることを確認します。たとえば、`**/*.test.ts` に一致する TypeScript テストファイルが対象になります。
5. このプロジェクトの単体テスト作成に関する指示を確認します。
6. `.github/instructions/drizzle.instructions.md` を開き、末尾にある他の指示ファイル (`unit-tests.instructions.md` など) やプロジェクト内ファイルへのリンクを確認します。大きな指示を再利用しやすい小さなファイルに分け、コード生成時に参照する例を Copilot に示せます (パスはリポジトリルートではなく、指示ファイルからの相対パスです)。

> [!NOTE]
> `copilot-instructions.md` の **Code formatting requirements** セクションにはプロジェクトのコーディング標準が記載されていますが、コード内のドキュメントはまだ必須ではありません。次の手順で TSDoc のドキュメントコメントとファイルコメントヘッダーの規則を追加します。

## 変更用ブランチを作成する

ここでは、ベストプラクティスに沿って、変更用ブランチを作成します。

1. 前の演習で使った Codespace に戻ります。
2. <kbd>Control</kbd>+<kbd>`</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>`</kbd> (Windows/Linux) を押して新しいターミナルを開きます。
3. 次のコマンドで新しいブランチを作成し、切り替えます。

   ```bash
   git checkout -b custom-instructions
   ```

## 指示を更新する前に Copilot Chat を使う

> [!TIP]
> **Copilot Chat を開く**
>
> 以下の演習を始める前に Codespace に戻り、Copilot Chat パネルを開き、**New Chat** を選択して新しい会話を始めます。モードとモデルの選択は演習によって異なります。必要な場合は、各手順で説明します。

カスタム指示の影響を確認するため、まず現在の指示ファイルを使ってプロンプトを送信します。その後、指示を更新して同じプロンプトを再送信し、違いを確認します。

1. 前の演習で使ったエディタータブをすべて閉じ、Copilot が必要なコンテキストだけを取得するようにします。
2. `src/lib/publishers.ts` を開き、ヘルパーの配置先を Copilot に示します。
3. Chat ビューのエージェントドロップダウンから **Agent** を選択し、Copilot がファイルを変更できるようにします。

   ![Chat ビューのエージェント選択を示すスクリーンショット。](../../../_images/shared-chat-mode-selector.png)

4. 次のプロンプトを送信します。

   ```plaintext
   src/lib/publishers.ts を新規作成または更新し、各発行元の名前と ID を含む一覧を返すデータアクセスヘルパーを作成してください。ファイルの変更を適用してください。
   ```

5. Copilot はプロジェクトを調べてコードを更新します。多くの場合、ヘルパーファイルとテストの両方が変更されます。
6. 提案されたヘルパーが、`db` クライアントを最初の引数として受け取り、発行元の型付き配列を返す関数であることを確認します。これは `src/lib/*.ts` に適用される `.github/instructions/drizzle.instructions.md` のデータレイヤー規約に基づいています。
7. 提案されたコードに TSDoc のドキュメントコメントとファイルレベルのコメントヘッダーが**ない**ことを確認します。

> [!CAUTION]
> Copilot は確率的に動作するため、指示がなくてもドキュメントコメントを追加する場合があります。その場合も問題ありません。この演習の目的は、指示の更新後に一貫性がどう改善するかを確認することです。

## 新しいリポジトリ標準を追加する

`.github/copilot-instructions.md` は、プロジェクトレベルの情報を Copilot に提供するファイルです。コード提案の改善に役立つよう、リポジトリのコーディング標準を追加します。

1. `.github/copilot-instructions.md` を開き直します。
2. 27 行目付近にある **Code formatting requirements** セクションを探します。現在はコーディング標準が記述されていますが、生成コード内のドキュメント規則はありません。
3. 既存の標準の直下に、ファイルヘッダーと TSDoc のドキュメントコメントを追加する次の Markdown 行を追加します。

   ```markdown
   - Every exported function should have a TSDoc comment describing its purpose, parameters, and return value.
   - Before imports or any code, add a comment block to the file that explains its purpose.
   ```

4. `copilot-instructions.md` を保存します。

> [!TIP]
> 前のレッスンで確認したように、指示ファイルはリポジトリ全体のガイダンスを記述する `.github/copilot-instructions.md`、または特定の言語、ファイルの種類、タスクに使う `*.instructions.md` として作成できます。ドキュメントコメントの規則のようなプロジェクト全体の標準には、リポジトリレベルのファイルが適しています。

## プロンプトを再実行し、変更を確認する

1. Copilot Chat に戻り、**New Chat** を選択してコンテキストをクリアします。
2. `src/lib/publishers.ts` を再度開き、Copilot に適切なファイルを認識させます。
3. 前と**同じプロンプト**を送信します。

   ```plaintext
   src/lib/publishers.ts を新規作成または更新し、各発行元の名前と ID を含む一覧を返すデータアクセスヘルパーを作成してください。ファイルの変更を適用してください。
   ```

4. 提案されたファイルの先頭に、次のようなコメントブロックが追加されていることを確認します。

   ```typescript
   /**
    * Publisher data-access helpers for the Tailspin Toys Crowd Funding platform.
    * Provides functions to retrieve publisher information from the database.
    */
   ```

5. 提案された関数に、次のような TSDoc コメントが追加されていることを確認します。

   ```typescript
   /**
    * Returns a list of all publishers with their id and name.
    *
    * @param db - The Drizzle database client.
    * @returns A promise that resolves to an array of publisher objects.
    */
   ```

新しいプロジェクト標準を Copilot に伝え、次の演習で使うコードに適用できました。

## 指示をコミットしてブランチをプッシュする

指示ファイルも他のリポジトリアセットと同様に、ソース管理で管理します。指示と変更をブランチにコミットして、リポジトリにプッシュします。

1. <kbd>Control</kbd>+<kbd>`</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>`</kbd> (Windows/Linux) を押して、新しいターミナルウィンドウを開きます。
2. 次のコマンドを実行し、指示とヘルパーの変更が存在することを確認します。

   ```bash
   git status
   ```

3. ターミナルで指示の更新と、ヘルパーの基盤に関して Copilot が変更したすべてのファイルをステージングしてコミットします。

   ```bash
   git add .github/copilot-instructions.md src/lib/publishers.ts
   # If git status shows additional supporting updates (for example tests), add those files too.
   git commit -m "Add doc comment standards and publishers helper foundation"
   ```

4. 次のコマンドでブランチをリポジトリにプッシュします。

   ```bash
   git push -u origin custom-instructions
   ```

## プルリクエストを作成してマージする

ブランチをプッシュしたら、プルリクエスト (PR) を作成し、バックログにあるドキュメント標準の Issue に関連付けます。手動で行う代わりに、すでにプロジェクトに接続されている GitHub ツールを使って Copilot に依頼できます。Issue を見つけ、PR で Issue を解決するようにして、マージを依頼します。

> [!NOTE]
> Copilot に GitHub 上の操作を依頼するのはこれが初めてです。プロジェクトテンプレートでは GitHub のツールがワークスペースにあらかじめ接続されています。Copilot が初めてツールを使うときに、VS Code でサインインを求められることがあります。プロンプトに従って認証してください。後の演習では、この接続の仕組みである Model Context Protocol について確認します。

1. Codespace で Copilot Chat を開きます。
2. <kbd>Control</kbd>+<kbd>Command</kbd>+<kbd>I</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>I</kbd> (Windows/Linux) を押して Copilot Chat ビューを開き、エージェント選択で **Agent** が選ばれていることを確認します。
3. 指示ファイルの更新に関する Issue を探し、現在のブランチから PR を作成して、指示の更新と今後のフィルタリング作業に向けた発行元ヘルパーの基盤の両方を説明するよう Copilot に依頼します。

   ```plaintext
   指示ファイルの更新に関する Issue を見つけてください。現在のブランチから新しい PR を作成し、その Issue を解決することを明記してください。また、今後のフィルタリング作業に向けて発行元ヘルパーの基盤も追加したことを記載してください。
   ```

4. Copilot が Issue を探して PR を作成します。
5. Copilot に GitHub 操作の許可を求められた場合は、コマンドを確認して、適切であれば **Allow** を選択します。
6. PR が作成されたら、PR をマージしてローカルのブランチを `main` に戻すよう、次のプロンプトで依頼します。

   ```plaintext
   PR を main にマージしてください。その後、ローカルで main に戻り、最新のコードをプルして最新状態にしてください。
   ```

7. Copilot に GitHub 操作やシェルコマンドの許可を求められた場合は、コマンドを確認して、適切であれば **Allow** を選択します。

GitHub Copilot の支援でプルリクエストを作成し、マージできました。

## まとめと次のステップ

このプロジェクトで Copilot が指示ファイルからコンテキストを取得する仕組みを確認し、VS Code の Copilot Chat を使って次の作業を行いました。

- リポジトリのカスタム指示とパスで対象を限定した指示ファイルが Copilot に届く仕組みを確認しました。
- 現在の指示でコード生成プロンプトを送り、発行元ヘルパーの提案を確認しました。
- `.github/copilot-instructions.md` に新しいリポジトリ全体の標準を追加しました。
- 同じプロンプトを再送信し、提案されたコードが新しい標準に従うことを確認しました。
- 次の演習で基盤として使えるよう、指示の更新とヘルパーを `main` にコミットしました。

次は、Copilot がコードベース全体に新機能を追加するときに、この指示が役立つことを[agent mode で確認します][next-lesson]。

## リソース

- [GitHub Copilot のカスタマイズに使う指示ファイル][instruction-files]
- [カスタム指示の作成に関するベストプラクティス][instructions-best-practices]
- [Copilot のカスタム指示を改善する 5 つのヒント][copilot-instructions-five-tips]
- [GitHub Copilot の個人用カスタム指示][personal-instructions]
- [Awesome Copilot — 指示ファイルなどのリソース集][awesome-copilot]

[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-agent-mode/
[instruction-files]: https://code.visualstudio.com/docs/copilot/copilot-customization
[instructions-best-practices]: https://docs.github.com/enterprise-cloud@latest/copilot/using-github-copilot/coding-agent/best-practices-for-using-copilot-to-work-on-tasks#adding-custom-instructions-to-your-repository
[personal-instructions]: https://docs.github.com/copilot/customizing-copilot/adding-personal-custom-instructions-for-github-copilot
[copilot-instructions-five-tips]: https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/
[awesome-copilot]: https://github.com/github/awesome-copilot
