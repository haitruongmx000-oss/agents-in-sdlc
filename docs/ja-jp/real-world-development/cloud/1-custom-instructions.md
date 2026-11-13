---
title: "演習 1 - カスタム指示 (Cloud agent)"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 前のレッスン: 前提条件][previous-lesson] · [次のレッスン: Copilot cloud agent →][next-lesson]

人工知能 (AI) を使うときは、コンテキストが重要です。特定の方法でタスクを進める必要がある場合や、Copilot に伝えておくべき背景情報がある場合は、そのコンテキストを利用できるようにします。[指示ファイル][instruction-files]を使うと、何をしてほしいかだけでなく、どのように作業してほしいかも Copilot に伝えられます。

この演習では、次の内容を学習します。

- リポジトリのカスタム指示とパスで対象を限定した指示ファイルによって、プロジェクト固有のコンテキスト、コーディングガイドライン、ドキュメント標準が Copilot に伝わる仕組みを確認する。
- `.github/copilot-instructions.md` に新しいリポジトリ全体の標準を追加する。

> [!NOTE]
> VS Code および CLI のハーネスとは異なり、この演習ではプロンプトの変更前後を比較しません。Copilot cloud agent は GitHub Issue に対して非同期で動作するため、リアルタイムの比較が難しいからです。後続の演習で cloud agent が作成したプルリクエストをレビューし、指示ファイルの影響を確認します。

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

## 新しいリポジトリ標準を追加する

ここで行う編集は、コード内のドキュメントを TSDoc コメントとファイルレベルのコメントヘッダーで記述する、プロジェクト全体の規則を追加することです。次の演習で Issue を割り当てると、cloud agent がこの規則に従います。

編集を始める前に、作業用のブランチを作成します。cloud agent は Issue の対象ブランチにある指示を読み込みますが、変更をコミットする際にもブランチを使います。

1. Codespace のターミナルで、新しいブランチを作成して切り替えます。

   ```bash
   git checkout -b custom-instructions-cloud
   ```

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

## 指示の更新をコミット、プッシュ、マージする

cloud agent は、Issue の対象となるブランチから指示ファイルを読み込みます。次の演習で Issue を Copilot に割り当てると、`main` からブランチが作成されるため、指示の変更を `main` にマージしておく必要があります。

1. ステージングしてコミットします。

   ```bash
   git add .github/copilot-instructions.md
   git commit -m "Add doc comment and file-header standards to copilot instructions"
   ```

2. ブランチをプッシュします。

   ```bash
   git push -u origin custom-instructions-cloud
   ```

3. github.com で `custom-instructions-cloud` から `main` へのプルリクエストを開き、マージします。これにより、次の演習で cloud agent が作業するときに、新しい指示を読み込めます。

> [!TIP]
> このワークショップで `main` に直接作業する場合は、ブランチを省略して `main` にコミットしてもかまいません。実際のプロジェクトの進め方に近づけるため、ここではブランチを使っています。

## まとめと次のステップ

この演習では、次の作業を行いました。

- リポジトリのカスタム指示とパスで対象を限定した指示ファイルによって、プロジェクト固有のコンテキスト、コーディングガイドライン、ドキュメント標準が Copilot に伝わる仕組みを確認しました。
- `.github/copilot-instructions.md` にリポジトリ全体の標準を追加しました。

残りのハーネスでは、cloud agent が作成するプルリクエストを通して、この標準が適用される様子を確認します。

次は、[最初の Issue を Copilot cloud agent に割り当てます][next-lesson]。

## リソース

- [GitHub Copilot のカスタマイズに使う指示ファイル][instruction-files]
- [カスタム指示の作成に関するベストプラクティス][instructions-best-practices]
- [Copilot のカスタム指示を改善する 5 つのヒント][copilot-instructions-five-tips]
- [Awesome Copilot — 指示ファイルなどのリソース集][awesome-copilot]

[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-cloud-agent/
[instruction-files]: https://docs.github.com/copilot/customizing-copilot/about-customizing-github-copilot-chat-responses
[instructions-best-practices]: https://docs.github.com/enterprise-cloud@latest/copilot/using-github-copilot/coding-agent/best-practices-for-using-copilot-to-work-on-tasks#adding-custom-instructions-to-your-repository
[copilot-instructions-five-tips]: https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/
[awesome-copilot]: https://awesome-copilot.github.com/
