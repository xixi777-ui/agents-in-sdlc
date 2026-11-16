---
title: "演習 1 - カスタム命令 (VS Code)"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 前のレッスン: 前提条件][previous-lesson] · [次のレッスン: Agent mode →][next-lesson]

生成 AI を使ううえで、コンテキストは重要です。タスクを特定の方法で実行する必要がある場合や、Copilot が背景情報を知っておく必要がある場合は、そのコンテキストを参照できるようにします。Copilot に特定のコンテキストを伝える方法はいくつかあります。中でも重要なのは、コード生成に関するガイダンスを提供する[命令ファイル][instruction-files]です。

この演習では、次のことを行います。

- リポジトリのカスタム命令とパス指定の命令ファイルを使って、プロジェクト固有のコンテキスト、コーディング ガイドライン、ドキュメント標準が Copilot に伝わる方法を確認します。
- 現在の命令を使って、フィルタリングに使う最初のデータ スライス (発行元ヘルパー) を生成します。
- `.github/copilot-instructions.md` に新しいリポジトリ全体の標準を追加します。
- 同じプロンプトを再実行し、生成されたコードに新しい標準が反映されることを確認します。
- 命令の更新とフィルタリングの基礎部分を `main` にコミットし、次の演習で Copilot が更新されたガイダンスを使えるようにします。

> [!CAUTION]
> 生成されるコードは、設定した標準から逸脱することがあります。Copilot の動作は確率的です。この演習では、命令を更新した後の動作の*傾向*を確認します。出力が文字単位で完全に一致することは期待しません。

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

- `.github/copilot-instructions.md` は、リポジトリに対する**すべての**リクエストで Copilot に送信される単一の命令ファイルです。Copilot への多くのチャットや CLI リクエストに関係する、プロジェクト レベルのコンテキストを記載します。たとえば、技術スタック、開発対象の概要、ベスト プラクティス、全体に適用する指針などです。
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

このリポジトリに含まれる命令ファイルを確認します。中心となる `copilot-instructions.md` が 1 つと、さまざまなタスクに対応する `*.instructions.md` ファイルがあります。エディターまたは GitHub Web UI で開いてください。

1. `.github/copilot-instructions.md` を開きます。
2. プロジェクトの簡単な説明と、**Agent notes**、**Code standards**、**Scripts**、**Repository Structure** などのセクションを確認します。**Code standards** にある **GitHub Actions Workflows** の項目も確認してください。これらの内容は Copilot とのすべてのやり取りに適用されます。
3. `.github/instructions` フォルダーを開いて内容を確認します。Astro ファイル、Drizzle データレイヤー、テストなどの命令があります。
4. `.github/instructions/unit-tests.instructions.md` を開きます。先頭の `applyTo` フィールドが、適用対象のファイルを決める glob (リポジトリのルートからの相対パス) を設定していることを確認します。ここでは、`**/*.test.ts` に一致するファイルなど、すべての TypeScript テスト ファイルが対象です。
5. このプロジェクトでユニットテストを作成する際の命令を確認します。
6. `.github/instructions/drizzle.instructions.md` を開き、末尾までスクロールします。`unit-tests.instructions.md` などの命令ファイルや、プロジェクトの既存ファイルへのリンクを確認します。こうしたリンクを使うと、大きな命令セットを再利用しやすい小さなファイルに分け、コード生成の参考例を Copilot に示せます。ここに記載されたパスは、リポジトリのルートではなく命令ファイルからの相対パスです。

> [!NOTE]
> `copilot-instructions.md` の **Code formatting requirements** セクションではプロジェクトのコーディング標準を説明していますが、コード内のドキュメントについてはまだ要件がありません。次の手順で、TSDoc コメントとファイル コメントのルールを追加します。

## 変更用のブランチを作成する

ここではベスト プラクティスに従い、変更用のブランチを作成します。

1. 前の演習で使った codespace に戻ります。
2. <kbd>Control</kbd>+<kbd>`</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>`</kbd> (Windows/Linux) を押して、新しいターミナルを開きます。
3. 次のコマンドを実行してブランチを作成し、切り替えます。

   ```bash
   git checkout -b custom-instructions
   ```

## 命令を更新する前に Copilot Chat を使う

> [!TIP]
> **Copilot Chat を開く**
>
> 以下の演習を始める前に、codespace に戻って Copilot Chat パネルを開き、**New Chat** を選択して新しい会話を始めます。モードやモデルの選択は演習によって異なります。必要な場合は各手順で説明します。

カスタム命令の影響を確認するため、まず現在の命令ファイルのままでプロンプトを送信します。後で命令を更新し、同じプロンプトを再送信して違いを確認します。

1. Copilot に必要なコンテキストだけを読み込ませるため、前の演習で開いたエディターのタブをすべて閉じます。
2. `src/lib/publishers.ts` を開き、Copilot にヘルパーの配置場所を示します。
3. Chat ビューのエージェント ドロップダウンで **Agent** を選択し、Copilot がファイルを変更できるようにします。

   ![Chat ビューのエージェント選択メニュー](../../../_images/shared-chat-mode-selector.png)

4. 次のプロンプトを送信します。

   ```plaintext
   Create or update src/lib/publishers.ts with a data-access helper that returns a list of all publishers with the name and id for each. Apply the file changes.
   ```

5. Copilot はプロジェクトを調べて、ヘルパー ファイルとテストなどにコードの変更を適用します。
6. 提案されたヘルパーが、最初の引数として `db` クライアントを受け取る型付き関数であり、型付きの発行元配列を返すことを確認します。これは `.github/instructions/drizzle.instructions.md` (`src/lib/*.ts` に適用) のデータレイヤーの慣習に基づいています。
7. 提案されたコードに、TSDoc のドキュメント コメントとファイル単位のコメント ヘッダーが**ない**ことに注目してください。

> [!CAUTION]
> Copilot の動作は確率的なため、命令がなくてもドキュメント コメントが追加されることがあります。その場合も問題ありません。命令を追加した後に、より一貫して反映されることがこの演習の目的です。

## 新しいリポジトリ標準を追加する

`.github/copilot-instructions.md` は Copilot にプロジェクト レベルの情報を提供します。コードの提案を改善するため、リポジトリのコーディング標準を明記しましょう。

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

## プロンプトを再実行して変化を確認する

1. Copilot Chat に戻り、**New Chat** を選択してコンテキストをクリアします。
2. `src/lib/publishers.ts` を選択して、Copilot に対象ファイルを示します。
3. 先ほどと**同じプロンプト**を送信します。

   ```plaintext
   Create or update src/lib/publishers.ts with a data-access helper that returns a list of all publishers with the name and id for each. Apply the file changes.
   ```

4. 提案されたファイルの先頭に、次のようなコメント ブロックが追加されていることを確認します。

   ```typescript
   /**
    * Publisher data-access helpers for the Tailspin Toys Crowd Funding platform.
    * Provides functions to retrieve publisher information from the database.
    */
   ```

5. 提案された関数に、次のような TSDoc コメントが含まれていることを確認します。

   ```typescript
   /**
    * Returns a list of all publishers with their id and name.
    *
    * @param db - The Drizzle database client.
    * @returns A promise that resolves to an array of publisher objects.
    */
   ```

Copilot を新しいプロジェクト標準に従うように導き、次の演習の土台となる実際のコードに適用しました。

## 命令をコミットしてブランチをプッシュする

命令ファイルもほかのプロジェクト ファイルと同じように、ソース管理を使って管理します。変更をコミットして、リポジトリにブランチをプッシュしましょう。

1. <kbd>Control</kbd>+<kbd>`</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>`</kbd> (Windows/Linux) を押して、新しいターミナルを開きます。
2. ターミナルから次のコマンドを実行し、命令の変更とヘルパーの変更が存在することを確認します。

   ```bash
   git status
   ```

3. ターミナルで命令の更新と、ヘルパーの基礎部分のために Copilot が変更したすべてのファイルをステージしてコミットします。

   ```bash
   git add .github/copilot-instructions.md src/lib/publishers.ts
   # git status に追加ファイル (テストなど) が表示される場合は、それらも追加します。
   git commit -m "Add doc comment standards and publishers helper foundation"
   ```

4. ターミナルからリポジトリにブランチをプッシュします。

   ```bash
   git push -u origin custom-instructions
   ```

## プルリクエストを作成してマージする

ブランチをプッシュしたら、バックログのドキュメント標準に関する issue と関連付けてプルリクエストを作成します。手動で行う代わりに、すでにプロジェクトに接続されている GitHub ツールを使って Copilot に依頼できます。Copilot に issue を検索して PR を作成し、issue をクローズするように指定してから、マージを依頼します。

> [!NOTE]
> Copilot に GitHub 操作を依頼するのはこれが初めてです。プロジェクト テンプレートでは、GitHub のツールがすでにワークスペースに接続されています。Copilot が初めてツールを使うときに、VS Code で GitHub へのサインインを求められることがあります。画面の指示に従ってください。この接続の仕組み (Model Context Protocol) は、後の演習で学びます。

1. codespace で Copilot Chat を開きます。
2. <kbd>Control</kbd>+<kbd>Command</kbd>+<kbd>I</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>I</kbd> (Windows/Linux) を押して Copilot Chat ビューを開き、エージェント選択メニューで **Agent** が選択されていることを確認します。
3. 命令ファイルを更新する issue を Copilot に検索させ、現在のブランチから PR を作成するよう依頼します。PR には命令の更新と発行元ヘルパーの基礎部分の両方を記述します。

   ```
   Find the issue related to updating the instructions file. Create a new PR from the current branch, highlight that the PR closes that issue, and include that we also added the publishers helper foundation for the upcoming filtering work.
   ```

4. Copilot が issue を見つけて PR を作成するのを待ちます。
5. Copilot に GitHub 操作を許可するよう求められたら、コマンドを確認し、問題がなければ **Allow** を選択します。
6. PR が作成されたら、次のプロンプトを使って Copilot に PR のマージと `main` ブランチへの復帰を依頼します。

   ```
   Merge the PR into main. Then return to main locally, and pull the latest code so we are up to date.
   ```

7. Copilot に GitHub 操作とシェル コマンドの実行を許可するよう求められたら、コマンドを確認し、問題がなければ **Allow** を選択します。

GitHub Copilot の支援を使って、プルリクエストを作成しマージできました。

## まとめと次のステップ

このプロジェクトの命令ファイルから Copilot がコンテキストを取得する方法を確認し、VS Code の Copilot Chat を使って次のことを行いました。

- 現在の命令を使ってコード生成プロンプトを送信し、Copilot の出力を確認しました。
- `.github/copilot-instructions.md` に新しいリポジトリ全体の標準を追加しました。
- 同じプロンプトを再実行し、提案されたコードに新しい標準が反映されることを確認しました。
- 命令の更新とヘルパーの基礎部分を `main` にコミットし、次の演習で使えるようにしました。

次は、Copilot がコードベース全体に機能を追加するときに、[agent mode][next-lesson]でこれらの命令を使います。

## リソース

- [GitHub Copilot のカスタマイズに使う命令ファイル][instruction-files]
- [効果的なカスタム命令を作成するためのベスト プラクティス][instructions-best-practices]
- [Copilot のカスタム命令を改善する 5 つのヒント][copilot-instructions-five-tips]
- [GitHub Copilot の個人用カスタム命令][personal-instructions]
- [Awesome Copilot — 命令ファイルなどのリソース集][awesome-copilot]

[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-agent-mode/
[instruction-files]: https://code.visualstudio.com/docs/copilot/copilot-customization
[instructions-best-practices]: https://docs.github.com/enterprise-cloud@latest/copilot/using-github-copilot/coding-agent/best-practices-for-using-copilot-to-work-on-tasks#adding-custom-instructions-to-your-repository
[personal-instructions]: https://docs.github.com/copilot/customizing-copilot/adding-personal-custom-instructions-for-github-copilot
[copilot-instructions-five-tips]: https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/
