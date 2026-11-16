---
title: "演習 4 - エージェントを監視して管理する"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

ここまでの数回の演習で、ユーザー体験の改善や機能追加を目的とする 3 つの別々のタスクを Copilot cloud agent に依頼しました。cloud agent は非同期かつ自律的に作業しますが、タスクを監視することも重要です。

cloud agent に割り当てたタスクを管理する方法はいくつかあります。たとえば、GitHub.com の[エージェント ページ][agents-page]を使う方法があります。この Mission control では、オープンなプルリクエスト (PR) があるすべてのエージェント タスクを確認できます。実行内容を調べたり、進行中のセッションに追加の指示をして作業を導いたりできます。

このレッスンでは、次のことを行います。

- cloud agent のタスクを監視するために、エージェント ページを確認します。
- 進行中のセッションに追加機能を依頼し、作業を案内します。

## シナリオ

エージェントに高コントラスト モードを作成してもらった後、チームはライトモードも追加するよい機会だと考えました。サイトのスタイル更新と切り替え機能をすでに実装しているため、ライトモードも含めるのが自然です。高コントラストに加えてライトモードも追加するよう、エージェントの作業を案内します。

## Copilot cloud agent のタスクを確認する

Copilot cloud agent に割り当てたすべてのタスクの現在の状態を確認しましょう。

1. [Copilot agents ページ](https://github.com/copilot/agents)に移動します。
2. メイン ペインと左ペインの両方にあるタスク一覧を確認します。Copilot に割り当てた次のタスクが表示されます。
   - コードのドキュメントを更新する。
   - ゲーム変更用の API を生成する。
   - Web サイトに高コントラスト モードを追加する。
3. 実行中のタスクを選択します。Copilot が実行したタスクを確認します。次のような操作が含まれることがあります。
   - リポジトリからコードをチェックアウトする。
   - Copilot の作業環境を作成する。
   - MCP サーバーをセットアップする。
   - 割り当てられたタスクを完了するための各種手順を実行する。

> [!NOTE]
> 表示される手順は、Copilot の作業状況や採用した方法によって異なります。

4. 右側に PR のペインが表示されていることも確認します。ここでは、PR や変更されたファイルを確認し、追加の監視を行えます。

## cloud agent の作業を案内する

実行中のタスクを確認できたので、高コントラスト モードの作業中にライトモードの切り替えも追加するよう依頼します。

1. 高コントラスト モードを追加するセッションを選択します。タイトルは、Copilot が付けた名前や作業状態によって異なります。

   ![Mission control に表示されるアクセシビリティ セッション](../../../_images/ex6-accessibility-session.png)

2. セッションがセットアップを終えて作業を開始したことが表示されるまで、数分待ちます。以下のようなメッセージが表示されたら作業が始まっています。
3. **Steer active session while Copilot is working** ダイアログに、次のプロンプトを入力します。

   ```plaintext
   While we are working on a high contrast mode, let's also add a light mode. There should be a switch for this mode as well where users can select their desired display mode.
   ```

   ![Copilot agents ページの cloud agent タスク。作業中のセッションに追加指示を送るダイアログが強調表示されています。](../../../_images/ex6-steer-cloud-agent-task.png)

4. <kbd>Enter</kbd> を押してプロンプトを送信します。
5. Copilot がプロンプトを受け取り、作業に取り込む様子を確認します。

## Copilot の作業を続ける

以前と同様に、Copilot は更新されたタスクに取り組みます。メッセージを送信した時点で作業中の手順を終えた後、新しい依頼をワークフローに取り込みます。

これにも数分かかります。これまでに学んだことや確認したことを振り返りましょう。

## まとめと次のステップ

Copilot agents ページを確認しました。これは、Copilot cloud agent のタスクを監視し、案内するための中心的な場所です。この Mission control では、進行中または完了したすべてのタスクを追跡し、実行中の作業をレビューし、進行中のタスクの範囲を調整したり追加の指示を与えたりできます。

次のことを学びました。

- Mission control とエージェント ページを使って、cloud agent のタスクを監視しました。
- 進行中のセッションに追加機能を依頼しました。

Copilot がアクセシビリティ機能を実装している間に、次は Copilot が作成したプルリクエストを[反復的に改善][next-lesson]します。

## リソース

- [Copilot agents ページ][agents-page]
- [カスタムエージェントについて][custom-agents]

---

| [← 前のレッスン: カスタムエージェント][previous-lesson] | [次のレッスン: Copilot の作業を反復的に改善 →][next-lesson] |
|:--|--:|

[previous-lesson]: ../3-custom-agents/
[next-lesson]: ../5-iterating/
[agents-page]: https://github.blog/changelog/2025-07-03-agents-page-for-copilot-coding-agent-in-public-preview
[custom-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
