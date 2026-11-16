---
title: "演習 2 - Copilot Agent Mode で新機能を追加する"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

アプリケーションへの小さな更新でも、通常は複数のファイルを変更し、テストを実行するなどの作業が必要です。開発者は通常、必要なファイルを探し、変更を加え、テストを実行し、問題をデバッグし、変更漏れのファイルを見つけて、さらに更新するという作業を繰り返します。作業は次々と続きます。

そこで Copilot Agent Mode を使います。

Copilot Agent Mode は、IDE 内でより自律的に作業するよう設計されています。開発者と同様に、まず既存のプロジェクト構造を調査し、必要な更新を行い、テストなどのタスクを実行し、発見した問題を自動的に修正します。Agent Mode を使って、サイトに新しい機能を追加する方法を確認しましょう。

> [!NOTE]
> 名前は似ていますが、agent mode と cloud agent は異なる用途向けに作られています。Agent mode は IDE 内でタスクを実行するため、すばやいフィードバックと対話が可能です。Cloud agent はチームの一員のように非同期で作業する AI ペア プログラマーとして設計されており、issue やプルリクエストを通じてやり取りします。

この演習では、次のことを学びます。

- Copilot Agent Mode がプロジェクトを調査し、関連ファイルを特定して、複数ファイルにまたがる変更を行う方法。
- GitHub Copilot Agent Mode が UI とデータレイヤーに新機能を実装する方法。
- コードベースにマージする前に、Copilot Agent Mode が生成した変更とテストをレビューする方法。

## シナリオ

ゲームの一覧が増えているため、ユーザーがカテゴリや発行元で絞り込めるようにしたいと考えています。前の演習では発行元ヘルパーを追加しました。次は、残りのデータレイヤー、UI、テストの作業を Copilot Agent Mode で完了します。

## Tailspin Toys Web サイトを実行する

変更を加える前に、Tailspin Toys Web サイトの現在の機能を確認しましょう。

この Web サイトは、開発者向けテーマのボードゲーム クラウドファンディング プラットフォームです。ゲームの一覧を表示し、詳細を確認できます。単一の Astro アプリで、ビルド時に静的 HTML としてページを生成します。ページは、Drizzle ORM を使ってフロントマターからローカル SQLite データベースを直接クエリします。別のバックエンド API やクライアント側 UI フレームワークはありません。再利用可能なデータアクセス ヘルパーは `src/lib/` に置き、操作性は標準 DOM API を使った小規模で範囲の限定された Astro `<script>` で追加します。

### Web サイトを起動する

簡単に実行できるように、単一の Astro 開発サーバーを起動する `npm run dev` タスクが用意されています。次の手順で GitHub Codespace から実行できます。

1. 前の演習の続きである codespace に戻ります。`main` ブランチにいるはずです。
2. Codespace 内で <kbd>Control</kbd>+<kbd>\`</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>\`</kbd> (Windows/Linux) を押して、新しいターミナルを開きます。
3. 次のコマンドを実行して、フィルタリング作業用のブランチを作成し、切り替えます。

   ```bash
   git checkout main
   git pull
   git checkout -b filtering-vscode
   ```

4. 次のコマンドを実行して Web サイトを起動します。

   ```bash
   npm run dev
   ```

   Astro 開発サーバーの準備ができると、次のような URL を含むメッセージが表示されます。

   ```bash
   🚀 Tailspin Toys is ready!
      Astro server: http://localhost:4321
      Press Ctrl-C to stop.
   ```

> [!NOTE]
> `http://localhost:4321` を開くよう促すダイアログが表示された場合は、**x** を選択して閉じます。

5. ターミナルで <kbd>Command</kbd> (Mac) または <kbd>Ctrl</kbd> (Windows/Linux) を押しながら Astro サーバーのアドレス `http://localhost:4321` を選択し、Web サイトを開きます。

> [!NOTE]
> codespace で localhost の URL を選択すると、`https://<your-codespace-name>-4321.app.github.dev/` に自動的にリダイレクトされます。codespace にトンネルが開き、Web サーバーにアクセスできるようになります。

### Web サイトを確認する

Web サイトを起動したら、機能を確認します。主な機能は次のとおりです。

- **ホームページ**: ボードゲームのタイトル、画像、説明を一覧表示します。
- **ゲーム詳細ページ**: ゲームを選択すると、タイトル、説明、発行元、カテゴリなどの詳細が表示されます。

## Copilot でバックログを確認する

> [!TIP]
> **Copilot Chat を開く**
>
> 以下の演習を始める前に、codespace に戻って Copilot Chat パネルを開き、**New Chat** を選択して新しい会話を始めます。モードやモデルの選択は演習によって異なります。必要な場合は各手順で説明します。

Web サイトの初期実装は動作しますが、新しい機能を追加して改善したいと考えています。まずバックログを確認しましょう。テンプレートからプロジェクトをセットアップすると、issue のバックログが自動的に作成されます。GitHub Copilot にそれらの issue を表示してもらいます。

1. Chat ビューのエージェント ドロップダウンで **Agent** を選択します。**Agent** エージェントは、自律的に複数ファイルにまたがる変更を計画・実装し、ターミナル コマンドを実行してツールを呼び出します。

   ![Chat ビューのエージェント選択メニュー](../../../_images/shared-chat-mode-selector.png)

2. 利用可能なモデルの一覧から **Claude Sonnet 4.5** を選択します。

> [!CAUTION]
> このワークショップの作成者が、特定のモデルを推奨しているわけではありません。ワークショップの作成時には Claude Sonnet 4.5 を使ったため、手順にも記載しています。提案の一貫性を高め、学習しやすくすることを目指しています。ただし、LLM は確率的に動作するため、ワークショップに記載された内容とは異なる提案が表示されることがあります。これは通常の動作です。

> [!NOTE]
> 確率的な動作のため、Copilot が別の MCP コマンドを使うことがありますが、タスクは同じように完了できます。

3. 次のプロンプトを Copilot に送信して、バックログの issue を確認します。

   ```plaintext
   Please show me the backlog of items from my GitHub repository. Help me prioritize them based on those which will be most useful to the user.
   ```

4. **Continue** を選択して、すべての issue を一覧表示するコマンドを実行します。
5. Copilot が生成した issue の一覧を確認します。

Copilot がユーザーに最も役立つと思われる項目を優先順位付けしたことにも注目してください。

## 命令ファイルを確認する

Copilot にコードを生成してもらう前に、作業のコンテキストを伝えるために使う命令ファイルを確認します。Web サイトに機能を追加する際の考え方を記載した [ユーザー インターフェイス (UI)][ui-instructions] の命令ファイルを利用します。

1. codespace で `.github/instructions/ui.instructions.md` に移動します。
2. 機能追加の方針に関するガイダンスを確認します。次の内容が含まれます。
   - アーキテクチャの概要。
   - コンポーネントの設計、テスト容易性、アクセシビリティに関する原則。
   - 次のようなさまざまなファイル種別に関する命令ファイルへのリンク。
     - Astro
     - Tailwind CSS
     - Drizzle

> [!TIP]
> 命令ファイルから、ほかの命令ファイルやプロジェクト内のファイルを参照できます。パスは命令ファイルの場所からの相対パスです。命令を再利用しやすい小さな単位に分けて、Copilot に例やテンプレートを示せます。

[ui-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md

## フィルタリング機能を実装する

フィルタリングを完成させるには、少なくとも次の 3 つの更新が必要です。

- データレイヤー (`src/lib/`) で、残りのフィルタリング ロジックを追加または調整する。
- フィルタリング動作のテストを追加または更新する。
- ゲーム一覧ページにフィルタリング UI を追加する。

さらに、コードベースにマージする前にテストを実行し、すべて成功する必要があります。Copilot Agent Mode でこれらの作業を依頼できます。機能を追加しましょう。

1. 現在の Copilot 会話を続けるか、**New Chat** を選択して新しい会話を開始します。
2. **Add Context**、**Instructions**、**ui** の順に選択し、命令ファイルを追加します。

   ![UI 命令ファイルを選択する例](../../../_images/ex3-select-instructions-file.png)

3. Chat ビューのエージェント ドロップダウンで **Agent** が選択されていることを確認します。

   ![Chat ビューのエージェント選択メニュー](../../../_images/shared-chat-mode-selector.png)

4. モデルが **Claude Sonnet 4.5** のままであることを確認します。
5. バックログにある関連 issue に基づいて機能を実装するよう、次のプロンプトを使って Copilot に依頼します。

   ```plaintext
   Please update the site to include filtering by publisher and category based on the requirements from the related GitHub issue in the backlog. A publishers helper already exists from the previous exercise, so preserve and refine that work as needed while completing the remaining data-layer, UI, and tests. Ensure all tests are passing before completion. The server is already running, so you do not need to start it up.
   ```

6. Copilot がプロジェクトを調べ、機能に関係するファイルを見つける様子を確認します。データレイヤーのヘルパー、UI、テストを探し、ファイルを変更してテストを実行します。

   ![Copilot がプロジェクト ファイルを調査する画面](../../../_images/ex3-agent-mode-explores.png)

> [!NOTE]
> Copilot はプロジェクトの調査、変更、テストの実行などを行います。タスクやコードベースの複雑さによっては、数分かかることがあります。作業中、コード エディターに **Keep** と **Undo** のボタンが表示されることがあります。Copilot の作業が完了すると、すべての変更に **Keep** または **Undo** が表示されるため、作業中に選択する必要はありません。

7. Copilot から求められたら、**Continue** を選択してテストを実行します。

   ![Copilot Chat ペインでテスト実行の許可を求めるダイアログ](../../../_images/ex3-agent-mode-run-tests.png)

8. 作業中に一時停止したり、テストが失敗したりすることがあります。問題ありません。Copilot は、タスクを完了してエラーがないと判断するまで、コード生成とテストを繰り返します。

   ![Copilot Agent Mode の完了したチャット セッション](../../../_images/ex3-agent-mode-proposed-changes.png)

9. 生成されたコードに問題がないか確認します。

> [!CAUTION]
> Copilot などの AI ツールが生成したコードは、必ずレビューしてください。

10. Web サイトを表示しているブラウザーに戻り、新しい機能を試します。
11. 動作を確認してコードのレビューが終わったら、Copilot Chat ウィンドウで **Keep** を選択します。

## まとめと次のステップ

おめでとうございます。この演習では、GitHub Copilot Agent Mode を使って Tailspin Toys Web サイトに機能を追加する方法を確認しました。次のことを学びました。

- GitHub Copilot Agent Mode が UI とデータレイヤーに新機能を実装する方法。
- Copilot Agent Mode がプロジェクトを調査し、関連ファイルを特定して、複数ファイルにまたがる変更を行う方法。
- コードベースにマージする前に、Copilot Agent Mode が生成した変更とテストをレビューする方法。

次は[Playwright MCP サーバーで機能をテスト][next-lesson]し、プルリクエストを開きます。

### 追加の演習 — ページネーションを実装する

ゲームの一覧が増えると、ページネーションが必要になります。この演習で学んだことを使い、Copilot にページネーションを実装するよう依頼します。次の点を検討してください。

- 既存の命令ファイルを使うなど、現在のベスト プラクティスに従います。
- ページサイズをユーザーが選べるようにするか、固定にするかなど、ページネーションの動作を決めます。
- 希望する実装に必要なガイダンスをプロンプトに含めます。
- Copilot に変更を依頼し、コンテキストを与えて繰り返し改善する必要がある場合があります。これは Copilot と作業するときの通常の流れです。

## リソース

- [Copilot の ask、edit、agent mode: それぞれの役割と使い分け][choose-mode]
- [VS Code の agent mode][vs-code-agent-mode]

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-mcp/
[choose-mode]: https://github.blog/ai-and-ml/github-copilot/copilot-ask-edit-and-agent-modes-what-they-do-and-when-to-use-them/
[vs-code-agent-mode]: https://code.visualstudio.com/docs/copilot/chat/chat-agent-mode
