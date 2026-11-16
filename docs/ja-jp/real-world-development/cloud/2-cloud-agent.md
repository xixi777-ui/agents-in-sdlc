---
title: "演習 2 - GitHub Copilot cloud agent"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 前のレッスン: カスタム命令][previous-lesson] |
|:--|

技術的負債に悩んでいない組織は、ほとんどないでしょう。未解決のセキュリティ問題、更新が必要なレガシー コード、実装する時間がなくバックログに残ったままの機能リクエストなどがあります。GitHub Copilot の cloud agent は、コードの更新や機能追加などのタスクを自律的に実行できます。作業が完了すると、人間の開発者がレビューできるドラフト PR を作成します。これにより、面倒な作業を任せて開発を加速し、開発者はより大きな課題に集中できます。

Copilot cloud agent を使って、次の内容を確認します。

- コード生成環境のカスタマイズ。
- 操作を安全に行う方法。
- 範囲を明確に定めた issue の重要性。
- issue を Copilot に割り当てる方法。

## シナリオ

Tailspin Toys には解決したい技術的負債があります。サイトの初期バージョンを作成した開発業者はドキュメントを適切に整備せず、現在、ドキュメントがまったく不足しています。まず、アプリケーション内のエクスポートされたすべての関数に TSDoc のドキュメント コメントを追加したいと考えています。

また、デザイン チームはゲームを見つけやすくしたいと考えています。各ゲームの詳細ページに、同じカテゴリに属するほかのゲームを関連ゲームとして表示し、閲覧を続けられるようにします。まだ完成度の高い実装は必要なく、受け入れテストで UX を試せれば十分です。この作業は現在ブロッカーですが、ほかに優先度の高い issue があります。

こうしたタスクは優先順位が下がりやすく、Copilot cloud agent に任せるのに適しています。Copilot cloud agent が非同期に作業する間、開発者は別のタスクに集中できます。その後、戻って Copilot の作業をレビューし、期待どおりの内容か確認できます。

## GitHub Copilot cloud agent について

[GitHub Copilot cloud agent](https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent#overview-of-copilot-cloud-agent-formerly-copilot-coding-agent) は、人間の開発者と同様にバックグラウンドでタスクを実行できます。人間の開発者に依頼する場合と同じように、[GitHub issue を Copilot に割り当てる][assign-issue]など、複数の方法で作業を依頼できます。割り当てると、Copilot は進捗を追跡するドラフト プルリクエストを作成し、環境をセットアップして、タスクに取り組み始めます。作業中でも完了後でも、Copilot のセッションを詳しく確認できます。提案された解決策のレビュー準備ができると、Copilot はプルリクエストであなたに通知します。

## 範囲を明確にした指示の重要性

GitHub Copilot に魔法はありません。考える作業を省略できるわけではありません。一見簡単な操作でも、詳しく調べると複雑なことがあります。[Copilot cloud agent に依頼するタスクの範囲を慎重に決めてください][cloud-agent-best-practices]。AI ペア プログラマーとして扱い、段階を踏んで学び、試し、必要に応じて調整します。生成 AI を加えても、ソフトウェア開発の基本は変わりません。

## このリポジトリのカスタム命令

これまでの演習では、カスタム命令と、Copilot を導く方法を学びました。カスタム命令の演習をすでに完了していれば、`.github/copilot-instructions.md` を使ったことがあるでしょう。まだの場合は、この機会に簡単に確認してください。Copilot cloud agent に作業を割り当てる前に、このリポジトリにすでに含まれる命令ファイルを読み取り専用で確認しておくと、後でその影響を見つけやすくなります。

リポジトリの GitHub Web UI、または起動済みの codespace で、次のファイルを開きます。

- `.github/copilot-instructions.md` - **Code standards** セクション、特に TypeScript の慣習と TSDoc コメントに関する要件を確認します。
- `.github/instructions/unit-tests.instructions.md` - `applyTo` フロントマターを確認します。この命令は `**/*.test.ts` に一致するファイルに適用されます。

次のセクションで `Code lacks documentation` issue を cloud agent に割り当てたら、作成されたプルリクエストに TSDoc コメント、TypeScript の慣習、ファイル コメントが反映されているかを確認します。これらの命令ファイルの影響は、後の演習で PR をレビューするときにもう一度取り上げます。

## Copilot cloud agent の開発環境をセットアップする

コードを作成するときは、誰が作業するかにかかわらず、通常は特定の環境やセットアップ スクリプトが必要です。タスクを Copilot に割り当てる場合も同様です。Copilot はソフトウェア エンジニアのように作業します。

cloud agent は GitHub Actions を作業環境として使います。`.github/workflows/copilot-setup-steps.yml` に設定する[専用のセットアップ ワークフロー][setup-workflow]を作成すると、作業を開始する前に開発ツールと依存関係を使えるようにできます。このワークショップの流れを円滑に進めるため、事前に設定されています。単一の Astro アプリに必要な Node.js、プロジェクトの依存関係、エンドツーエンドテスト用のブラウザー バイナリ、マイグレーションおよびシード済みの SQLite データベースを Copilot が利用できるようにします。

```yaml
name: "Copilot Setup Steps"

# リポジトリの "Actions" タブからセットアップ手順をテストできます。
on: workflow_dispatch

env:
  ASTRO_TELEMETRY_DISABLED: "1"

jobs:
  copilot-setup-steps:
    runs-on: ubuntu-latest
    # ここで実行する手順に必要な、最小限の権限を設定します。Copilot は自身の操作用トークンを受け取ります。
    permissions:
      # たとえば依存関係のインストールなど、セットアップ手順の一部としてリポジトリをクローンする場合は、`contents: read` 権限が必要です。セットアップ手順でクローンしない場合、手順の完了後に Copilot が自動的にクローンします。
      contents: read
    steps:
      - name: Checkout code
        uses: actions/checkout@v5

      # フロントエンド / アプリのセットアップ - Node.js (アプリ全体は Astro と Drizzle/libSQL で構成されています)
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

      # ビルドとテスト用に、ローカル SQLite データベースをマイグレーションしてシードします。
      - name: Set up the database
        run: npm run db:setup
```

ほかの GitHub Actions ワークフロー ファイルと同じように見えますが、いくつか重要な点があります。

- `copilot-setup-steps` という単一のジョブが含まれています。このジョブは、Copilot がプルリクエストの作業を始める前に GitHub Actions で実行されます。
- `workflow_dispatch` トリガーが設定されているため、リポジトリの **Actions** タブからワークフローを手動実行できます。Copilot が実行するのを待たずに、正常に実行されるかテストするのに便利です。

## ドキュメントを追加する

ドキュメントの重要性は広く理解されていますが、情報が古かったり不足していたりするプロジェクトは少なくありません。こうした技術的負債は放置されやすく、生産性を下げ、コードベースの保守や新しい開発者の参加を難しくします。Copilot はドキュメントの作成に適しているため、この issue は Copilot cloud agent に依頼するのにちょうどよいタスクです。バックグラウンドで必要なドキュメントを生成してもらい、後の演習で結果をレビューします。

1. 新しいブラウザー タブで github.com のリポジトリに移動します。
2. **Issues** タブを選択します。
3. **New issue** を選択して、新しい issue のダイアログを開きます。
4. **Blank issue** を選択して issue を作成します。
5. **Title** に `Code lacks documentation` と入力します。
6. **Description** に次のテキストを入力します。

   ```plaintext
   Our organization has a requirement that functions and methods include TSDoc doc comments where helpful. Unfortunately, recent updates haven't followed this standard. We need to update the existing code to ensure doc comments are included where they clarify behavior.
   ```

7. **Create** を選択して issue を作成します。
8. 右側にある **Assign to Copilot** を選択して、割り当てダイアログを開きます。

   ![Copilot に issue を割り当てる画面](../../../_images/shared-assign-copilot.png)

9. **Assign** を選択します。
10. **Pull Requests** タブを選択します。
11. 新しく生成されたプルリクエスト (PR) を開きます。タイトルは `[WIP]: Code lacks documentation` のような名前になります。PR が一覧に表示されない場合は、少し待ってからブラウザーを更新します。
12. 数分後、Copilot が todo リストを作成したことを確認します。

> [!NOTE]
> Copilot の todo リストが PR に表示されるまで数分かかることがあります。Copilot は前述のワークフローを実行して環境を作成し、プロジェクトを分析して、問題に取り組む方法を決めています。

13. todo リストと、Copilot が実行するタスクを確認します。
14. プルリクエストのタイムラインを下にスクロールし、Copilot が issue の作業を開始したことを示す更新を確認します。
15. **View session** ボタンを選択します。

   ![Copilot セッションの表示](../../../_images/ex4-view-session.png)

> [!CAUTION]
> 更新された表示を確認するには、ウィンドウの再読み込みが必要な場合があります。

16. ライブ セッションをスクロールして、Copilot の作業を確認します。コードを調査して状態を把握し、計画を考えて判断を下し、コードを作成する様子を確認できます。

これには数分かかることがあります。Copilot cloud agent の主な目的の 1 つは、非同期にタスクを実行して、ほかの作業に集中できるようにすることです。この機能を活用するため、もう 1 つのタスクも Copilot cloud agent に割り当ててから、アプリケーションの機能追加に取り組みます。

## ゲーム詳細ページに関連ゲームのセクションを追加する

GitHub Copilot cloud agent の大きな利点は作業を分担できることです。デザイン チーム向けの関連ゲームのセクションは、時間がかからない作業かもしれませんが、その時間をほかのタスクに使えます。Copilot cloud agent に割り当てましょう。

1. github.com のリポジトリに戻ります。
2. **Issues** タブを選択します。
3. **New issue** を選択して、新しい issue のダイアログを開きます。
4. **Blank issue** を選択して空のテンプレートを使います。
5. **Title** に `Show related games on the game details page` と入力します。
6. **Description** に次のテキストを入力します。

   ```markdown
   We want to help people discover more games by showing related games on each game's details page. The design team wants to explore the UX and do some acceptance testing. Our requirements are:

   - Add a data-access helper in `src/lib/` that returns other games in the same category as a given game, excluding that game
   - Show a "Related games" section on the game details page that uses the helper
   - Handle the case where a game has no related games
   - There should be unit tests created for the new helper
   - Before creating the PR, ensure all tests pass
   ```

7. **Create** を選択して issue を作成します。
8. 右側にある **Assign to Copilot** を選択して、割り当てダイアログを開きます。

   ![Copilot に issue を割り当てる画面](../../../_images/shared-assign-copilot.png)

9. **Assign** を選択します。

まもなく、issue の最初のコメントに 👀 が表示されます。Copilot が作業を開始したことを示します。

![目の絵文字で issue の作業中であることを示す Copilot](../../../_images/ex4-issue-eyes-emoji.png)

Copilot は 2 つ目の依頼に取り組んでいます。Copilot cloud agent はソフトウェア エンジニアと同じように作業するため、常に監視する必要はありません。完了してからレビューできます。次は、カスタムエージェントの作成と使用を確認します。

## まとめと次のステップ

[GitHub Copilot cloud agent][copilot-agents]について学びました。cloud agent に issue を割り当てると、非同期に作業してもらえます。技術的負債への対応、新機能の作成、別のフレームワークへのコード移行などを Copilot に依頼できます。

次のことを学びました。

- コード生成環境をカスタマイズする。
- 操作を安全に行う方法。
- 範囲を明確に定めた issue の重要性。
- issue を Copilot に割り当てる方法。

cloud agent がバックグラウンドで作業している間に、次はカスタムエージェントの作成と使用に取り組みます。[Copilot cloud agent は MCP サーバーも使用でき][cloud-agent-mcp]、カスタム命令も利用できます。これらは前のモジュールで確認しました。

## リソース

- [Copilot cloud agent について][copilot-agents]
- [GitHub issue を Copilot に割り当てる][assign-issue]
- [Copilot cloud agent セットアップ ワークフローのベスト プラクティス][cloud-agent-best-practices]

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
