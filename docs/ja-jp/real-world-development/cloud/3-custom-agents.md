---
title: "演習 3 - カスタムエージェント"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

## カスタムエージェントとは

GitHub Copilot の[カスタムエージェント][custom-agents-concept]を使うと、開発ワークフローにおける特定のタスクや分野に合わせて、専門的な AI アシスタントを作成できます。リポジトリの `.github/agents` フォルダーに Markdown ファイルを置いてエージェントを定義すると、特定の種類の作業を効果的に進めるための、対象を絞った命令、ベスト プラクティス、コーディング パターン、分野固有の知識を Copilot に提供できます。チームの知識を再利用可能なエージェントとして形式化できます。たとえば、[WCAG][wcag] への準拠を確認するアクセシビリティ エージェント、安全なコーディング慣行に従うセキュリティ エージェント、一貫したテスト パターンを維持するテスト エージェントなどです。

カスタムエージェントは、プロジェクトの `.github/agents` フォルダー内の Markdown ファイル、または `~/.copilot/agents` 内のグローバル ファイルで定義します。各ファイルには、少なくとも `name` と `description` を含む YAML フロントマターと、エージェントの動作、専門知識、命令を定義する Markdown プロンプトを記述します。

### カスタムエージェントとエージェント スキルの違い

カスタムエージェントと[エージェント スキル][agent-skills-concept]には、役割が一部重なるところがあります。どちらも主に Markdown ファイルで定義し、AI に作業の方法を指示します。最も分かりやすい違いは、**カスタムエージェントが作業者、スキルがツール**であることです。

カスタムエージェントは独自のコンテキスト ウィンドウを持ち、スキルやほかのエージェントを調整しながら作業します。この演習では、アクセシビリティのカスタムエージェントがアクセシビリティ ガイドラインに沿ってサイトをレビューし、更新します。その作業の一部として、プルリクエストのワークフローやテスト実行・管理用のスキルを呼び出すこともできます。

> [!NOTE]
> カスタムエージェントの作成に唯一の正解はありません。AI のほかの使い方と同様に、利用する環境やシナリオに合わせて試し、改善してください。

[custom-agents-concept]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
[agent-skills-concept]: https://docs.github.com/copilot/concepts/agents/about-agent-skills
[wcag]: https://www.w3.org/WAI/standards-guidelines/wcag/

カスタムエージェントを使って、次のことを学びます。

- カスタムエージェントの定義方法。
- カスタムエージェントへのタスク割り当て。

## シナリオ

Tailspin Toys は、視覚能力や好みにかかわらず、すべてのユーザーがクラウドファンディング プラットフォームを利用できるようにすることに取り組んでいます。最近、現在のダークテーマはテキストと背景色のコントラストが十分でないため、読みにくいと感じるユーザーがいることが分かりました。このアクセシビリティ上の課題に対応するため、デザイン チームは、ユーザーがオンとオフを切り替えられる高コントラスト モードを求めています。

アクセシビリティは重要なため、できるだけ早く実装したいと考えています。カスタムエージェントを使って機能を生成します。

## アクセシビリティ カスタムエージェントを確認する

アクセシビリティ用のカスタムエージェントは、あらかじめ作成されています。Copilot をどのように導くか、その内容を確認しましょう。

codespace に戻り、アクセシビリティ カスタムエージェントのファイルを確認します。

1. `.github/agents/accessibility.md` を開きます。
2. `name` と `description` の YAML フロントマターを確認します。

> [!CAUTION]
> カスタムエージェントには、`name` と `description` を含むフロントマターが必要です。

3. 続くセクションを読み、次の内容を確認します。
   - アクセシブルな Web サイトのコード生成における主な責務。
   - アクセシビリティのベスト プラクティス。
   - HTML、CSS、JavaScript のコード例。
   - よくある問題や間違い。

## issue を作成して割り当てる

Mission control は、環境内のエージェントを扱う中心的な場所です。Copilot cloud agent にタスクを割り当て、進捗を監視し、追加の指示をして作業の方向を調整できます。Copilot に高コントラスト モードの作成を依頼しましょう。

1. リポジトリに移動します。
2. **Issues** タブを選択します。
3. **New issue** を選択して、新しい issue のダイアログを開きます。
4. **Blank issue** を選択して、新しい issue を作成します。
5. **Title** に `Add high contrast mode to website` と入力します。
6. **Description** に次のテキストを入力します。

   ```plaintext
   We need a high contrast mode for the site. There should be a toggle for high contrast which the user can set. It should store the setting in local storage on the browser.
   ```

7. **Create** を選択して issue を作成します。
8. 右側にある **Assign to Copilot** を選択し、割り当てダイアログを開きます。
9. カスタムエージェントの一覧から **Accessibility agent** を選択します。

   ![カスタムエージェントとアクセシビリティを選択した Copilot cloud agent の割り当て画面](../../../_images/ex5-select-custom-agent.png)

10. **Assign** を選択します。
11. Copilot はバックグラウンドでタスクを開始します。

## まとめと次のステップ

GitHub Copilot の[カスタムエージェント][custom-agents]について学びました。カスタムエージェントは、特定のタスクや分野に合わせた専門的な AI アシスタントです。チームの知識や標準を再利用可能なエージェントに形式化すると、特定の種類の作業をより効果的に進めるよう Copilot を導けます。

次のことを学びました。

- カスタムエージェントの定義方法。
- カスタムエージェントへのタスク割り当て。

Copilot が高コントラスト モードを実装している間に、次は Mission control から[エージェント セッションを監視して案内][next-lesson]します。

## リソース

- [カスタムエージェントについて][custom-agents]
- [組織でカスタムエージェントを使うための準備][org-custom-agents]
- [エンタープライズでカスタムエージェントを使うための準備][enterprise-custom-agents]

---

| [← 前のレッスン: GitHub Copilot cloud agent][previous-lesson] | [次のレッスン: エージェントの監視と管理 →][next-lesson] |
|:--|--:|

[previous-lesson]: ../2-cloud-agent/
[next-lesson]: ../4-managing-agents/
[custom-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
[org-custom-agents]: https://docs.github.com/copilot/how-tos/administer-copilot/manage-for-organization/prepare-for-custom-agents
[enterprise-custom-agents]: https://docs.github.com/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-agents/prepare-for-custom-agents
