---
title: "演習 5: GitHub Copilot の作業を反復的に改善する"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 前のレッスン: エージェントの管理][previous-lesson] |
|:--|

## 作業をレビューする

この演習では、ユーザー体験の改善や機能追加を目的とする複数のタスクで、GitHub Copilot と作業してきました。コードにドキュメントを追加し、デザイン チームが反復的に改善できる関連ゲーム機能を作成し、ハイコントラスト モードとライトモードの切り替えなど、アクセシビリティ機能を実装するよう依頼しました。コードの変更を確認し、必要に応じて Copilot にフィードバックを伝えて改善してもらいましょう。

### シナリオ

何度も説明してきたように、生成 AI を加えてもソフトウェア設計と DevOps の基本は変わりません。生成されたコードをレビューし、通常の DevOps プロセスに沿って進める必要があります。チーム全体によるレビューを有効にする前に、ドキュメント、関連ゲーム機能、アクセシビリティ機能を作成するよう GitHub Copilot が提案した内容を確認します。

## セキュリティと GitHub Copilot cloud agent

Copilot cloud agent は非同期に、監督を受けずにタスクを実行するため、安全を確保するセキュリティ上の制約があります。具体的には、次のとおりです。

- Copilot はリポジトリへの読み取りアクセス権を持ち、書き込みできるのは作業に使うブランチ**のみ**です。
- Cloud agent は GitHub Actions 内で動作します。作業用の一時的な専用環境を作成します。
- GitHub Actions ワークフローを実行するには、人間による承認が必要です。
- [外部リソースへのアクセスは、デフォルトで制限されています](https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall)。MCP サーバーも対象です。

## 生成されたドキュメントをレビューする

まず、GitHub Copilot cloud agent が作成した最初のプルリクエスト (PR) を確認します。コードにドキュメントを追加する内容です。github.com の標準的な PR インターフェイスを使います。

> [!NOTE]
> PR を確認する際に、GitHub Copilot がファイアウォールでブロックされているという警告が表示されることがあります。Copilot は、外部 MCP サーバーへの呼び出しを含む外部リソースへのアクセスがデフォルトで制限されているため、これは**想定どおりの動作**です。必要であれば、Copilot cloud agent の[ファイアウォールをカスタマイズまたは無効化][agent-firewall]できます。

1. github.com のリポジトリに戻ります。
2. **Pull Requests** を選択して、プルリクエストの一覧を開きます。
3. **Add missing documentation** など、より詳しいタイトルの PR を開きます。

> [!NOTE]
> Copilot がまだタスクを実行中の場合、プルリクエストに **[WIP]** フラグが付いています。その場合は、Copilot が作業を完了するまで待ちます。数分かかることがあります。休憩したり、これまでに学んだことを振り返ったりしてください。

4. PR の準備ができたら **Files changed** タブを選び、変更をレビューします。

   ![Files changed タブ](../../../_images/shared-pr-files-changed.png)

5. 新たに更新されたコードを確認します。TSDoc のドキュメント コメントやほかのドキュメントが追加されています。変更の内容は状況によって異なります。

   変更を確認するときは、演習 2 の最初に確認したカスタム命令ファイルに基づく、TSDoc コメント、TypeScript の慣習、コメント ヘッダーを探します。

6. 更新内容をレビューし問題がなければ、**Conversation** タブに戻り、下にスクロールします。
7. ワークフローの承認待ちを示す表示を確認します。
8. ワークフローが承認待ちの場合は、**Approve and run workflows** を選択します。

   ![ワークフローを承認して実行する](../../../_images/shared-approve-workflows.png)

9. ワークフローが PR のチェック セクションでキューに登録されたことを確認します。正常に進めば、単一の Astro アプリに関するプロジェクトのチェックがすべて成功します。完了まで数分かかることがあります。

## GitHub Copilot に変更を依頼する

プルリクエストで Copilot と作業することは、一方的なやり取りではありません。チームのほかのメンバーと同じように、PR のコメントやコードへのインライン コメントで Copilot をタグ付けできます。Copilot はそのコメントを読み、対応するための新しいセッションを開始します。結果は確率的に変わるため、指定した文章をそのまま使う必要はありません。Copilot に依頼できる変更の例を挙げます。

- 各コード ファイルの先頭に、ファイルの役割を簡潔に説明するコメント ヘッダーを追加する。
- TypeScript と Astro のファイルに TSDoc のドキュメント コメントを追加する。
- Astro アプリの構造を説明する README を作成する。

1. **@copilot** をほかのユーザーと同じようにタグ付けして、生成されたドキュメントの変更を依頼するコメントを追加します。上記の例を使うか、コードベースのドキュメントについて別の変更を提案します。
2. **View Session** を選択して、Copilot の作業を確認します。Copilot が更新用の新しいセッションを開始することに注目してください。
3. **Back to pull request** を選択すると、プルリクエストに戻れます。

   ![プルリクエストに戻る](../../../_images/ex7-back-to-pr.png)

4. Copilot が変更を完了したら、プルリクエストに新しいコミットが表示されます。
5. **Files changed** タブを選び、変更をレビューします。

納得できるまで、Copilot に変更を繰り返し依頼してかまいません。内容に問題がなければ、ドラフト PR をレビュー可能な状態に変更して、`main` ブランチにマージできます。

![PR をレビュー可能な状態に変更する](../../../_images/ex7-ready-for-review.png)

## 関連ゲーム機能をレビューする

次は、ゲーム詳細ページに関連ゲームを表示する issue を解決するため、Copilot が作成した PR を確認します。

1. GitHub.com のリポジトリに戻ります。
2. **Pull Requests** タブを選択します。
3. **Show related games on the game details page** など、より詳しいタイトルの PR を選択します。
4. **Files changed** タブを選択して、生成されたコードをレビューします。
5. 更新内容をレビューし問題がなければ、**Conversation** タブに戻り、下にスクロールします。
6. ワークフローの承認待ちを示す表示を確認します。
7. ワークフローが承認待ちの場合は、**Approve and run workflows** を選択します。

   ![ワークフローを承認して実行する](../../../_images/shared-approve-workflows.png)

8. ワークフローが PR のチェック セクションでキューに登録されたことを確認します。正常に進めば、単一の Astro アプリに関するプロジェクトのチェックがすべて成功します。完了まで数分かかることがあります。
9. **任意:** Codespace でこのブランチに切り替え、関連ゲーム機能を手動でテストすることもできます。Codespace に移動し、ターミナルを開いて次のコマンドを実行します。`<branch-name>` は Copilot が作成したブランチ名 (例: **copilot/fix-8**) に置き換えます。

   ```bash
   git fetch origin
   git checkout <branch-name>
   ```

Copilot が関連ゲーム機能を作成しました。これまでと同じように、Copilot cloud agent に更新を依頼しながら、反復的に作業を進められます。たとえば、表示する関連ゲームの数を調整することや、コメント ヘッダーと TSDoc のドキュメント コメントを追加することを依頼できます。注意点として、この作業はカスタム命令を更新**する前**に割り当てたものです。ほかの PR と同様に、**Conversation** タブにコメントを追加すると、Copilot がそれを読み、新しいセッションを開始して対応します。

## アクセシビリティ機能をレビューする

最後に、アクセシビリティ カスタムエージェントを使って実装したアクセシビリティ機能をレビューします。この PR には、演習 3 で割り当てたハイコントラスト モードと、演習 4 の Mission control から追加を依頼したライトモードの両方が含まれるはずです。

1. GitHub.com のリポジトリに戻ります。
2. **Pull Requests** タブを選択します。
3. **Add high contrast mode to website** など、より詳しいタイトルの PR を選択します。

> [!NOTE]
> Copilot がまだタスクを実行中の場合、プルリクエストに **[WIP]** フラグが付いています。その場合は、Copilot が作業を完了するまで待ちます。数分かかることがあります。

4. **Files changed** タブを選択し、生成されたコードをレビューします。
5. 実装を確認します。特に、次の点を確認してください。
   - モード切り替え用の UI コンポーネント
   - ユーザー設定を維持するためのローカル ストレージの使用
   - ハイコントラスト モードとライトモードに関する CSS またはスタイルの変更
   - アクセシビリティ属性 (ARIA ラベル、キーボード ナビゲーションなど)
   - モード切り替えを管理する JavaScript/TypeScript コード

6. 更新内容をレビューし問題がなければ、**Conversation** タブに戻り、下にスクロールします。
7. ワークフローの承認待ちを示す表示を確認します。
8. ワークフローが承認待ちの場合は、**Approve and run workflows** を選択します。

   ![ワークフローを承認して実行する](../../../_images/shared-approve-workflows.png)

9. ワークフローが PR のチェック セクションでキューに登録されたことを確認します。正常に進めば、単一の Astro アプリに関するプロジェクトのチェックがすべて成功します。完了まで数分かかることがあります。
10. **任意:** Codespace でこのブランチに切り替え、アクセシビリティ機能を手動でテストできます。Codespace に移動し、ターミナルを開いて次のコマンドを実行します。`<branch-name>` は Copilot が作成したブランチ名に置き換えます。

   ```bash
   git fetch origin
   git checkout <branch-name>
   ```

   その後、アプリケーションを起動し、ブラウザーでハイコントラスト モードとライトモードの切り替えをテストします。ページを再読み込みしても設定が維持されることを確認します。

カスタムアクセシビリティ エージェントが、Copilot にアクセシビリティのベスト プラクティスに沿った実装を促したことに注目してください。アクセシビリティ上の懸念や改善点があれば、前の PR と同じようにコメントで **@copilot** をタグ付けし、更新を依頼できます。

## 任意の演習 — 委任を続ける

cloud agent は、実際のバックログのタスクを引き渡し、自分がほかの作業に集中できるときに効果を発揮します。練習として、リポジトリに issue をいくつか作成し、Copilot に割り当てます。たとえば、次のようなタスクがあります。

- ゲーム詳細ページに支援者向けの関心フォームを作成する。
- ゲーム一覧ページにページネーションを実装する。
- データアクセス ヘルパーに入力検証とエラー処理を追加する。

## まとめ

Cloud agent の一連の演習を完了しました。次のことを行いました。

- **このリポジトリに含まれるカスタム命令ファイルを確認し**、後で cloud agent の出力にどのように影響するかを確かめました。
- **issue を Copilot cloud agent に割り当て**、環境のセットアップ、計画、非同期の実行を確認しました。
- アクセシビリティ向けの**カスタムエージェントを作成して使い**、ハイコントラスト モードとライトモードの切り替えを追加しました。
- Copilot agents ページを Mission control として使い、進行中のセッションを監視して案内しました。
- cloud agent が作成したプルリクエストをレビューして反復し、`@copilot` に変更を依頼してワークフローを承認しました。

## レビューと次のステップ

Cloud agent の一連の演習を完了しました。ここで試したことを補完する、ほかの環境も確認できます。

- 🖥️ **[VS Code 環境](../../vscode/)** — IDE から直接、Copilot Chat の agent mode と MCP 統合を試します。
- 💻 **[CLI 環境](../../cli/)** — Plan と Autopilot モード、エージェント スキル、カスタム QA エージェント、Playwright MCP、Agent Merge、cloud agent に戻る任意の `/delegate` ブリッジを使って、ターミナルからレビュー済みの変更を 2 つ実装します。

自分のリポジトリでは、次の方法を試してください。

- リファクタリングやテスト範囲の拡充を目的とする issue を cloud agent に割り当てる。
- 別の分野を担当するカスタムエージェントを作成する。
- 別の技術スタック向けに `copilot-setup-steps.yml` を設定する。

## リソース

- [GitHub Copilot][github-copilot]
- [Copilot エージェントについて][copilot-agents]
- [GitHub issue を Copilot に割り当てる][assign-issue]
- [Copilot cloud agent セットアップ ワークフローのベスト プラクティス][cloud-agent-best-practices]
- [Copilot cloud agent のファイアウォールを設定する][agent-firewall]

---

| [← 前のレッスン: エージェントの管理][previous-lesson] |
|:--|

[github-copilot]: https://github.com/features/copilot
[cloud-agent-overview]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[agent-firewall]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall

[previous-lesson]: ../4-managing-agents/
