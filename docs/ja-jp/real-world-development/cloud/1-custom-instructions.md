---
title: "演習 1 - カスタム命令 (Cloud agent)"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 前のレッスン: 前提条件][previous-lesson] · [次のレッスン: Copilot cloud agent →][next-lesson]

生成 AI を使ううえで、コンテキストは重要です。タスクを特定の方法で実行する必要がある場合や、Copilot が背景情報を知っておく必要がある場合は、そのコンテキストを参照できるようにします。[命令ファイル][instruction-files]を使うと、Copilot に「何を」するかだけでなく、「どのように」実行するかも伝えられます。

この演習では、次のことを行います。

- リポジトリのカスタム命令とパス指定の命令ファイルを使って、プロジェクト固有のコンテキスト、コーディング ガイドライン、ドキュメント標準が Copilot に伝わる方法を確認します。
- `.github/copilot-instructions.md` に新しいリポジトリ全体の標準を追加します。

> [!NOTE]
> VS Code や CLI の環境とは異なり、この演習ではプロンプトの実行前と実行後を比較しません。Copilot cloud agent は GitHub issue に対して非同期に動作するため、影響をリアルタイムで並べて示すのが難しいためです。cloud agent が生成するプルリクエストをこの環境の後半でレビューするときに、命令ファイルの影響を確認できます。

## 命令ファイル

### シナリオ

Tailspin Toys には、開発手法に関するガイドラインと要件があります。具体的には、次のとおりです。

- データレイヤーには、必ずユニットテストを用意します。
- UI はダークモードで、現代的な見た目にします。
- TSDoc のドキュメント コメントをコードに追加します。
- 各ファイルの先頭に、そのファイルの役割を説明するコメント ブロックを追加します。

命令ファイルを使って、Copilot がこれらの開発慣行に沿ってタスクを実行できるようにします。

### カスタム命令

カスタム命令を使うと、コーディングスタイルや要件を Copilot に伝え、より適切な提案やコードスニペットを得られます。好みのコーディング規則、ライブラリ、コメントの書き方などを指定できます。リポジトリ全体に適用する命令も、特定の種類のファイルやタスクに適用する命令も作成できます。

命令ファイルには次の 2 種類があります。

- `.github/copilot-instructions.md` は、リポジトリに対する**すべての**リクエストで Copilot に送信される単一の命令ファイルです。Copilot への多くのチャットや CLI リクエストに関係する、プロジェクトレベルのコンテキストを記載します。たとえば、技術スタック、開発対象の概要、ベスト プラクティス、全体に適用する指針などです。
- `.github/instructions/*.instructions.md` は、特定のタスクやファイルの種類に合わせて作成できます。TypeScript や Astro などの言語、UI コンポーネントやユニットテストの作成などのタスクに関するガイドラインを記載できます。

> [!NOTE]
> IDE では、命令ファイルは Copilot Chat のコード生成にのみ使われ、コード補完や次の編集候補には使われません。
>
> Copilot Chat、Copilot CLI、Copilot cloud agent は、リポジトリ レベルの命令と `applyTo` フロントマターを持つ `*.instructions.md` ファイルの両方を使ってコードを生成します。
>
> また、Copilot は AGENTS.md や CLAUDE.md など、ほかの標準形式の命令ファイルもサポートしています。[カスタム命令のサポート][custom-instructions-support]を参照してください。

### 命令ファイルを管理するためのベスト プラクティス

命令ファイルの作成について詳しく説明することは、このワークショップの範囲外です。ただし、サンプル プロジェクトには、代表的な構成例が含まれています。基本的には、次のようにします。

- `copilot-instructions.md` には、開発対象、プロジェクトの構造、全体に適用されるコーディング標準など、プロジェクト レベルの指針を記載します。
- `*.instructions.md` は、ファイル種別 (ユニットテスト、Astro コンポーネント、データレイヤーなど) や特定のタスクに関する指示に使います。
- 自然な言葉で、明確なガイダンスを記載します。コードの良い例と悪い例を示します。

AI の使い方に唯一の正解がないのと同じように、命令ファイルにも決まった構成はありません。プロジェクトで試しながら、最適な方法を見つけてください。

> [!TIP]
> GitHub Copilot を使うすべてのプロジェクトに、充実した命令ファイルを用意することをお勧めします。このプロジェクトには、[UI 更新][ui-instructions]や [Astro][astro-instructions] など、多様なタスク向けのファイルがあります。
>
> Copilot に命令ファイルを生成してもらうこともできます。各環境での操作方法は異なります (たとえば、VS Code の **Configure Chat → Generate Agent Instructions**、Copilot CLI の `/init`)。詳細が関係する場合は、利用中の環境のレッスンで説明します。
>
> テンプレートや出発点を探している場合は、命令ファイル、カスタムエージェントなどを集めたリポジトリ [awesome-copilot][awesome-copilot] を確認してください。

[ui-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md
[astro-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/astro.instructions.md
[awesome-copilot]: https://github.com/github/awesome-copilot
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support

## このプロジェクトのカスタム命令ファイルを確認する

このリポジトリに含まれる命令ファイルを確認します。中心となる `copilot-instructions.md` が 1 つと、さまざまなタスクに対応する `*.instructions.md` ファイルがあります。エディターまたは GitHub の Web UI で開いてください。

1. `.github/copilot-instructions.md` を開きます。
2. プロジェクトの簡単な説明と、**Agent notes**、**Code standards**、**Scripts**、**Repository Structure** などのセクションを確認します。**Code standards** にある **GitHub Actions Workflows** の項目も確認してください。これらの内容は Copilot とのすべてのやり取りに適用されます。
3. `.github/instructions` フォルダーを開いて内容を確認します。Astro ファイル、Drizzle データレイヤー、テストなどの命令があります。
4. `.github/instructions/unit-tests.instructions.md` を開きます。先頭の `applyTo` フィールドが、適用対象のファイルを決める glob (リポジトリのルートからの相対パス) を設定していることを確認します。ここでは、`**/*.test.ts` に一致するファイルなど、すべての TypeScript テスト ファイルが対象です。
5. このプロジェクトでユニットテストを作成する際の命令を確認します。
6. `.github/instructions/drizzle.instructions.md` を開き、末尾までスクロールします。`unit-tests.instructions.md` などの命令ファイルや、プロジェクトの既存ファイルへのリンクを確認します。こうしたリンクを使うと、大きな命令セットを再利用しやすい小さなファイルに分け、コード生成の参考例を Copilot に示せます。ここに記載されたパスは、リポジトリのルートではなく命令ファイルからの相対パスです。

> [!NOTE]
> `copilot-instructions.md` の **Code formatting requirements** セクションではプロジェクトのコーディング標準を説明していますが、コード内のドキュメントについてはまだ要件がありません。次の手順で、TSDoc コメントとファイル コメントのルールを追加します。

## リポジトリの標準を追加する

この演習で行う唯一の編集として、TSDoc コメントとファイル単位のコメントをコードに記載するプロジェクト全体のルールを追加します。cloud agent は、この環境の後半で割り当てる issue に取り組むときに、このルールを読み込みます。

編集する前に、作業用のブランチを作成します。cloud agent は issue の対象ブランチから命令を読み込みますが、この更新自体もきちんとコミットしておきます。

1. codespace のターミナルで、次のコマンドを実行してブランチを作成し、切り替えます。

   ```bash
   git checkout -b custom-instructions-cloud
   ```

前に説明したように、`.github/copilot-instructions.md` は Copilot にプロジェクト レベルの情報を提供します。コードの提案を改善するため、リポジトリのコーディング標準を記載しましょう。

1. `.github/copilot-instructions.md` をもう一度開きます。
2. 27 行目付近にある **Code formatting requirements** セクションを探します。プロジェクトのコーディング標準を説明していますが、コード内ドキュメントのルールがありません。そのため、生成されたヘルパーにドキュメント コメントがありませんでした。
3. 既存の標準の直後に、次の Markdown 行を追加し、ファイル コメントと TSDoc コメントを記述するよう Copilot に指示します。

   ```markdown
   - Every exported function should have a TSDoc comment describing its purpose, parameters, and return value.
   - Before imports or any code, add a comment block to the file that explains its purpose.
   ```

4. `copilot-instructions.md` を保存します。

> [!TIP]
> 前のレッスンで説明したように、命令ファイルはリポジトリ レベル (`.github/copilot-instructions.md`) に置いて全体に適用することも、特定の言語、ファイル種別、タスクに適用する `*.instructions.md` として作成することもできます。ドキュメント コメントのルールのようなプロジェクト全体の標準には、リポジトリ レベルのファイルが適しています。

## 命令の更新をコミット、プッシュ、マージする

cloud agent は、issue の対象ブランチにある命令ファイルを読み込みます。次の演習で Copilot に issue を割り当てると、`main` を起点にブランチが作成されます。cloud agent が命令の更新を使えるようにするには、変更を `main` にマージする必要があります。

1. ステージングしてコミットします。

   ```bash
   git add .github/copilot-instructions.md
   git commit -m "Add doc comment and file-header standards to copilot instructions"
   ```

2. ブランチをプッシュします。

   ```bash
   git push -u origin custom-instructions-cloud
   ```

3. github.com で `custom-instructions-cloud` から `main` に向けてプルリクエストを開き、マージします。次の演習で issue を割り当てるとき、cloud agent はこの命令を読み込みます。

> [!TIP]
> このワークショップで `main` に直接作業したい場合は、ブランチの作成を省略して、`main` に直接コミットしてもかまいません。実際のプロジェクトでの手順を模しているため、ここではブランチを使っています。

## まとめと次のステップ

このプロジェクトの命令ファイルから Copilot がコンテキストを取得する方法を確認し、リポジトリ全体に適用する新しい標準を `.github/copilot-instructions.md` に追加しました。この環境の残りの演習で cloud agent が作成するプルリクエストを確認すると、この標準が適用されていることが分かります。

次は、最初の issue を Copilot cloud agent に[割り当て][next-lesson]ます。

## リソース

- [GitHub Copilot のカスタマイズに使う命令ファイル][instruction-files]
- [効果的なカスタム命令を作成するためのベスト プラクティス][instructions-best-practices]
- [Copilot のカスタム命令を改善する 5 つのヒント][copilot-instructions-five-tips]
- [Awesome Copilot — 命令ファイルなどのリソース集][awesome-copilot]

[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-cloud-agent/
[instruction-files]: https://docs.github.com/copilot/customizing-copilot/about-customizing-github-copilot-chat-responses
[instructions-best-practices]: https://docs.github.com/enterprise-cloud@latest/copilot/using-github-copilot/coding-agent/best-practices-for-using-copilot-to-work-on-tasks#adding-custom-instructions-to-your-repository
[copilot-instructions-five-tips]: https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/
[awesome-copilot]: https://awesome-copilot.github.com/
