---
title: "レッスン 4 - カスタム命令で Copilot を導く"
description: "リポジトリの命令を確認し、ドキュメント標準を追加して、フィルタリングのコードに適用します。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

生成 AI を使ううえで、コンテキストは重要です。タスクを特定の方法で実行する必要があるなら、その指針を Copilot が参照できるようにします。[命令ファイル][instruction-files]では、必要なコードだけでなく、その構成方法も説明できます。フィルタリングを実装したので、Copilot が使った命令を確認し、ドキュメント標準を追加して、コードに適用します。

このレッスンでは、次のことを学びます。

- リポジトリの命令とパス指定の命令ファイルがエージェントにどのように適用されるかを確認します。
- コーディング標準を守れるよう、命令ファイルを更新します。
- 命令ファイルがコードに与える影響を確認します。

## シナリオ

Tailspin Toys には、開発手法に関するガイドラインと要件があります。具体的には、次のとおりです。

- コメントではコードを言い換えるのではなく、意図や自明でない判断を説明します。
- `db/` と `src/lib/` のエクスポートされた関数には、目的、パラメーター、戻り値を説明する TSDoc/JSDoc を記述します。該当する場合は、注入可能な `db` 引数も含めます。
- 再利用可能な Astro コンポーネントには `Props` の契約を記述し、関連コードが変更されたときにコメントも最新の状態に保ちます。
- 既存のフォーマットと lint のガイダンスを維持します。

命令ファイルを使って、Copilot がこれらの開発慣行に沿ってタスクを実行できるようにします。

## 命令ファイル

カスタム命令を使うと、コーディングスタイルや要件を Copilot に伝え、より適切な提案やコードスニペットを得られます。好みのコーディング規則、ライブラリ、コメントの書き方などを指定できます。リポジトリ全体に適用する命令も、特定の種類のファイルやタスクに適用する命令も作成できます。

命令ファイルには次の 2 種類があります。

- `.github/copilot-instructions.md` は、リポジトリに対する**すべての**リクエストで Copilot に送信される単一の命令ファイルです。多くのリクエストに関係するプロジェクトレベルの情報を記載します。
- `.github/instructions/*.instructions.md` は、特定の言語、ファイル種別、タスクに関するガイドラインを提供します。

> [!NOTE]
> ほかの命令形式やサポート状況は、環境によって異なります。特定の形式を使う前に、[カスタム命令のサポート リファレンス][custom-instructions-support]を確認してください。

## このプロジェクトのカスタム命令ファイルを確認する

スターター プロジェクトには、いくつかの命令ファイルが含まれています。変更を加える前に、まず既存のファイルを確認し、変更の影響を把握しましょう。

1. Codespace に戻ります。
2. Codespaces のエディター (ターミナルではありません) で `.github/copilot-instructions.md` を開きます。
3. プロジェクトの概要とコーディング指針を確認します。この命令は、このリポジトリでの Copilot とのすべてのやり取りに適用されます。
4. `.github/instructions` フォルダーを開き、ファイルを確認します。Astro ファイル、Drizzle データレイヤー、テストなどの命令があります。
5. `.github/instructions/unit-tests.instructions.md` を開きます。先頭の `applyTo` フィールドが、命令を適用するファイルを決める glob を設定していることを確認します。
6. `.github/instructions/drizzle.instructions.md` を開き、ほかの命令ファイルや既存プロジェクト ファイルへのリンクを確認します。大きな命令セットを再利用可能な小さなファイルに分け、参考例を Copilot に示せます。

## チームのガイダンスに合わせて命令ファイルを更新する

既存のファイルはよい出発点ですが、TypeScript に新しく追加するコードに TSDoc コメントを記述する指針がありません。`.github/copilot-instructions.md` の中核となる命令ファイルを更新します。

1. `.github/copilot-instructions.md` で、35 行目付近にある **Code formatting guidance** セクションを探します。
2. 最後の箇条書きとして、次を追加します。

   ```markdown
   - All new TypeScript should contain TSDocs comments for documentation purposes.
   ```

ファイルは自動的に保存されます。

## 更新したガイダンスを使う

Copilot CLI は会話の開始時にリポジトリの命令を読み込みます。更新した命令を反映させながら機能のコンテキストを保つため、保存後にフィルタリングの会話を終了して再開します。

1. フィルタリングの会話が開いている場合は、`/exit` を入力して終了します。Copilot CLI は会話を自動的に保存します。
2. リポジトリのルートで `game-filters-cli` ブランチをチェックアウトした状態から、保存した会話を再開します。

   ```bash
   copilot --resume
   ```

   プロンプトが表示されたら、フィルタリングの会話を選択し、`game-filters-cli` ブランチがチェックアウトされていることを確認します。
3. 更新したガイダンスを適用するよう Copilot に依頼します。

   ```plaintext
   We just updated our instructions and code guidance. Can you please update the code you generated to match that guidance?
   ```

4. `/diff` を入力し、変更された TypeScript ファイルを確認します。新しく生成された TSDoc コメントがコードを正確に説明していることを確認します。

## まとめと次のステップ

Copilot CLI が命令ファイルからコンテキストを取得する方法を確認し、新しい標準を機能に適用しました。具体的には、次のことを行いました。

- リポジトリの命令とパス指定の命令ファイルがエージェントにどのように適用されるかを確認しました。
- 命令ファイルを更新し、コーディング標準を守れるようにしました。
- 命令ファイルがコードに与える影響を確認しました。

次は、lint とテストを一貫して実行するため、再利用可能な[品質チェックのスキルをカスタマイズして実行][next-lesson]します。

## リソース

- [Copilot CLI にカスタム命令を追加する][instruction-files]
- [カスタム命令のサポート][custom-instructions-support]
- [効果的なカスタム命令を作成するためのベスト プラクティス][instructions-best-practices]
- [Awesome Copilot — 命令ファイルなどのリソース集][awesome-copilot]

[instruction-files]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot/add-custom-instructions
[instructions-best-practices]: https://docs.github.com/copilot/concepts/prompting/response-customization#writing-effective-custom-instructions
[awesome-copilot]: https://awesome-copilot.github.com/
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support
[previous-lesson]: ../3-agent-modes/
[next-lesson]: ../5-agent-skills/
